# NYXCoin
Building Your NYX Master Node

## Here is a Full Video Walk-through
Coming Soon

## Preperation

1. Get a VPS from a provider like DigitalOcean, Vultr, Linode, etc. 
   - Recommended VPS size at least 1gb RAM 
   - **It must be Ubuntu 16.04 (Xenial)**
2. Make sure you have a transaction of exactly 1000 NYX in your desktop cold wallet.

## Cold Wallet Setup Part 1

1. Open your wallet on your desktop.

   Click Settings -> Options -> Wallet
   
   Check "Enable coin control features"
   
   Check "Show Masternodes Tab"
   
   Press Ok (you will need to restart the wallet)
   
   ![Alt text](https://i.imgur.com/S6WJBOT.png "Wallet Settings")

   
   
   
2. Go to the "Tools" -> "Debug console"
3. Run the following command: `masternode genkey`
4. You should see a long key that looks like:
```
3HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg
```
5. This is your `private key`, keep it safe, do not share with anyone.




## VPS Setup

1. Log into your VPS
   - Windows users [follow this guide](https://www.digitalocean.com/community/tutorials/how-to-log-into-your-droplet-with-putty-for-windows-users) to log into your VPS.
2. Copy/paste these commands into the VPS and hit enter: (One Box At A Time)
```
apt-get -y update
```
```
apt-get -y upgrade
```
```
apt-get -y dist-upgrade
```
```
cd
```
```
apt-get -y install software-properties-common
```
```
apt-get -y install software-properties-common nano libboost-all-dev libzmq3-dev libminiupnpc-dev libssl-dev libevent-dev
```
```
apt-add-repository -y ppa:bitcoin/bitcoin
```
```
apt-get -y update
```
```
apt-get -y install \
    wget \
    git \
    unzip \
    libevent-dev \
    libboost-dev \
    libboost-chrono-dev \
    libboost-filesystem-dev \
    libboost-program-options-dev \
    libboost-system-dev \
    libboost-test-dev \
    libboost-thread-dev \
    libminiupnpc-dev \
    build-essential \
    libtool \
    autotools-dev \
    automake \
    pkg-config \
    libssl-dev \
    libevent-dev \
    bsdmainutils \
    libzmq3-dev \
    nano
```
```
apt-get -y update
```
```
apt-get -y upgrade
```
```
apt-get -y install libdb4.8-dev
```
```
apt-get -y install libdb4.8++-dev
```
```
mkdir nyx && cd nyx
```
```
cd /var
touch swap.img
chmod 600 swap.img
dd if=/dev/zero of=/var/swap.img bs=1024k count=2000
mkswap /var/swap.img
swapon /var/swap.img
echo "/var/swap.img none swap sw 0 0" >> /etc/fstab
```
```
wget http://latest.nyxcoin.org/nyx-0.12.1-linux64.tar.gz
```
```
tar -xzf nyx-0.12.1-linux64.tar.gz
```
```
rm nyx-0.12.1-linux64.tar.gz
```
```
cd nyx-0.12.1
```
```
chmod +x nyx*
mv nyx-cli nyx-qt nyx-tx nyxd /usr/local/bin 
cd ..
rm -r -f nyx-0.12.1
nyxd -daemon
```
```

```
```
cd /root/.nyx
nyx-cli stop
nano nyx.conf
```
Replace:
externalip=VPS_IP_ADDRESS
masternodeprivkey=WALLET_GENKEY
With your info!
```
rpcuser=ReplaceWithSomeUsernameOnlyAlphabeticWithoutSymbolsAndSpacesSameAsInWindowsNYXCONFfile
rpcpassword=ReplaceWithYourOwnUniqueLongPasswordWithoutSymbolsAndSpacesSameAsInWindowsNYXCONFfile
rpcallowip=127.0.0.1
rpcport=4331
listen=1
server=1
daemon=1
externalip=XXX.XXX.XXX.XXX
masternode=1
masternodeprivkey=ReplaceWithYourPrivKey
```
CTRL X to save it. Y for yes, then ENTER.
```
nyxd &
```

3.Use `watch nyx-cli getinfo` to check and wait til it's synced 

## Now were going to setup Sentinel
```
nyxd &
```
```
apt-get -y install virtualenv python-pip
```
```
git clone https://github.com/nyxpay/sentinel /sentinel
```
```
cd /sentinel
```
```
virtualenv venv
```
```
. venv/bin/activate
```
```
pip install -r requirements.txt
```
```
crontab -e
```
Hit 2. This will bring up an editor. Paste in the following at the bottom.
```
* * * * * cd /sentinel && ./venv/bin/python bin/sentinel.py >/dev/null 2>&1
```
CTRL X to save it. Y for yes, then ENTER.

Now use a watch-nyx-cli geinfo to check and wait til its synced (look for blocks number and compare to the block explorer. http://explorer.nyxcoin.org/)






## Cold Wallet Setup Part 2 

1. On your local machine open your `masternode.conf` file.
   Depending on your operating system you will find it in:
   * Windows: `%APPDATA%\Nyx\`
   * Mac OS: `~/Library/Application Support/Nyx/`
   * Unix/Linux: `~/.Nyx/`
   
   Leave the file open
2. Go to "Tools" -> "Debug console"
3. Run the following command: `masternode outputs`
4. You should see output like the following if you have a transaction with exactly 1000 NYX:
```
{
    "12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx": "0"
}
```
5. The value on the left is your `txid` and the right is the `vout`
6. Add a line to the bottom of the already opened `masternode.conf` file using the IP of your
VPS (with port 24126), `private key`, `txid` and `vout`:
```
mn1 1.2.3.4:24126 3xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 0 
```
7. Save the file, exit your wallet and reopen your wallet.
8. Go to the "Masternodes" tab
9. Click "Start All"

Cheers !!
