# Sonarr series management application with Jackett torrent provider tool for Raspberry Pi (debian)
This is a tutorial to install sonarr series downloader with jackett (provides lot of torrent site like ncore.cc) to raspberry pi.

## Install

## Install mono:

Sadly or not sadly, but Sonarr and Jackett written in .NET. To be able to use .NET applications in linux, you need to install mono.

Add keyserver:

```shell
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
```

Then add mono repository to sources:

```shell
echo "deb http://download.mono-project.com/repo/debian wheezy main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
```

After all, update sources and install mono with all dependencies:

```shell
sud apt-get update && sudo apt-get install libmono-cil-dev -y
```

## Install Sonarr

Good news. Sonarr has a repository and you can use aptitude for install or update.

Add keyserver:

```shell
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA5DFFC
```

Then add sonarr repository to sources:

```shell
echo "deb https://apt.sonarr.tv/ master main" | sudo tee -a /etc/apt/sources.list.d/sonarr.list
```

Then update sources and install Sonarr:

```shell
sud apt-get update && sudo apt-get install nzbdrone -y
```

Now modify folder owner to actual user:

```shell
sudo chown -R YOUR_USERNAME:YOUR_USERNAME /opt/NzbDrone
```

### Run Sonarr at startup

If you would like to run Sonarr at startup, put sonarr daemon to `/etc/init.d` and set daemon to run automatically (You need to change `TO_YOUR_USERNAME` to username who will run the daemon):

```shell
sudo cp ./daemons/nzbdrone /etc/init.d/nzbdrone &&
sudo sed -i -e "s#YOUR_USERNAME#TO_YOUR_USERNAME#g" /etc/init.d/nzbdrone &&
sudo chmod a+x /etc/init.d/nzbdrone &&
sudo update-rc.d nzbdrone defaults
```

## Install Jackett

Jackett has no official repository, but can update automatically with built in updater, don't worry about that.

First of all, you need to download the latest release:

```shell
jackettver=$(wget -q https://github.com/Jackett/Jackett/releases/latest -O - | grep -E \/tag\/ | awk -F "[><]" '{print $3}') &&
wget -q https://github.com/Jackett/Jackett/releases/download/$jackettver/Jackett.Binaries.Mono.tar.gz
```

Then extract and move files to destination folder, change owner on destination folder to the actual user:

```shell
tar -xvf Jackett* &&
sudo mkdir /opt/jackett &&
sudo mv Jackett/* /opt/jackett &&
rm -rf Jackett* &&
sudo chown -R YOUR_USERNAME:YOUR_USERNAME /opt/jackett
```

### Run Jackett at startup

If you would like to run Jackett at startup, put sonarr daemon to `/etc/init.d` and set daemon to run automatically (You need to change `TO_YOUR_USERNAME` to username who will run the daemon):

```shell
sudo cp ./daemons/jackett /etc/init.d/jackett &&
sudo sed -i -e "s#YOUR_USERNAME#TO_YOUR_USERNAME#g" /etc/init.d/jackett &&
sudo chmod a+x /etc/init.d/jackett &&
sudo update-rc.d jackett defaults
```

## Configure

### Jackett

After Sonarr and Jackett install, you need to configure jackett first, because jackett provides to be able to search in our favourite torrent sites. In my example I will configure to ncore.cc the bigest hungarian torrent site. 

Open Jackett (http://ip.address:9117) and click `+ Add indexer` button:
<center>![Add indexer](http://image.prntscr.com/image/3e71156044a248eaaf4f8a8857ddb217.png)</center>

In the list you can choose the actual torrent site (in my case, this is the nCore):
<center>![Choose indexer](http://image.prntscr.com/image/27e61028147a437091d9c0a175d465d2.png)</center>

The following popup you need to enter nCore user / pass, then click `Okay` button:
<center>![Enter credentials](http://image.prntscr.com/image/70d77906c7be4ed3bebdae463db5678b.png)</center>

When you added new indexer, you can test it:
<center>![Test credentials](http://image.prntscr.com/image/b3ce535f1e9c45b8af30ee1ab9afba44.png)</center>

If everything works, you will see this:
<center>![Working credentials](http://image.prntscr.com/image/c7b464bf3b424f9790c5411d6f93ce63.png)</center>

Jackett configuration is done. You will need two things in Sonarr.
 - Api key:
 <center>![Api key location](http://image.prntscr.com/image/1929351d3b8548c3a89977c8a22f6414.png)</center>
 - URL for Sonarr (If Sonarr and Jackett run same raspberry, you can chane the ip address to `localhost`, in my case: `http://ip.address:9117/torznab/ncore`, changed to `http://localhost:9117/torznab/ncore`):
 <center>![Sonarr url location](http://image.prntscr.com/image/30d39f84823445619a197950050f0980.png)</center>

### Sonarr

Now open Sonarr(http://ip.address:8989). First of all, we will configure Sonarr.

Click settings icon:
<center>![Settings](http://image.prntscr.com/image/66e49a0460844c26b205cd2b3f622ae4.png)</center>

Now configure torrent client in `Download Client` tab. Click the giant `+` button, to add a torrent client:
<center>![Add torrent client](http://image.prntscr.com/image/2ba3fea8009c421187bb13f49d182b7b.png)</center>

In the torrent section you can see Deluge. Awesome, because we just use Deluge :sunglasses:
In the next popup delete category input content, because that need to enable deluge label plugin. If deluge run on same raspberry you only enter the deluge web password, and click the `Test` button, then click `Save` button.
<center>![Deluge torrent client settings](http://image.prntscr.com/image/7e59bfbae5e64832873df4d336f30c0c.png)</center>

Now you can add torrent indexers at the `Indexers` tab. Click the giant `+` button, to add an indexer:
<center>![Add indexer](http://image.prntscr.com/image/b49a5db6ac8a48a49d1851b2c3ace408.png)</center>

In the popup you need to choose `Torznab` to add Jackett indexer:
<center>![Add torznab indexer](http://image.prntscr.com/image/eda26db33f39492e8fea28f5b4e39545.png)</center>

If you remember I told that, you need two information from Jackett, API key and URL. Now fill inputs, then click `Test` then click `Save`
<center>![Add nCore indexer](http://image.prntscr.com/image/598bbf090d034d5188c4366d8404878c.png)</center>

Finally you can configure `Profiles` if you want to download series in your language. You can add new profile, and choose available qualities, change language. In the new profile popup, the question marks helps you to understand what you configure that section.

In the `Media Management` tab, you can fine tuning Sonarr, to rename files, set folder permissions, etc.

Thats all, you can add series now. One thing you feell confuse is the path. Sonarr will put series to this folder so I recommend for you choose an empty folder. Sonar will monitor deluge, when a download is finished it will create hardlink (or move / copy file, based on media management setting)