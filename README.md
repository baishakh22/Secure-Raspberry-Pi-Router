# Secure-Raspberry-Pi-Router
This is a super crazy project that I have done so far. Raspberry Pi can do anything. So I choose to use Raspberry Pi as secure router when I'm using public unsecure internet over VPN. For this I'm going to install openWRT which is a Linux based open source router. I can use this router anywhere, coffeeshop, hotel, airbnb anywhere. It connect to a public wifi as a client and give you a secure internet access. 

## Before Start
1. We need a Raspberry Pi 4. Because it has almost everything I need. It has ethernet, USB and most importantly it has built-in wifi antena. So, here, I'm using Raspberry Pi 4, Model B, RAM 4GB, Single Core Processor
2. Micro SD card, cause Rasberry Pi doesn't have any built-in storage. 
3. A USB wifi adapter. (https://www.amazon.com/gp/product/B01KWQAQ00/ref=ppx_yo_dt_b_search_asin_title?tag=networkchuck-20&ie=UTF8&psc=1&geniuslink=true) even though pi has already built-in wifi, why I need another wifi adapter? Beacause I need two wifi interface. The wifi on the board which is built-in in my raspberry pi 4, it will connect to any public wifi available. Beside, the wifi adapter will be giving me wifi. It will brodcasting my SSID and what I connect to get access to the internet. 
4. A ethernet cable. 
![HERO-500x335](https://user-images.githubusercontent.com/93491482/169657499-50ac04c3-a552-4380-96ba-b0d4169c5671.png)

## Pi Image Burner
Raspberry Pi has own image burner which is fast and easy. We are goning to use that from their website. (https://www.raspberrypi.com/software/) ![Screenshot 2022-05-20 20 29 14](https://user-images.githubusercontent.com/93491482/169657651-244be4d8-9045-4dd9-9084-c499ee474cfd.png)
Then go to openWRT website and Download the installation file that machetes my raspberry pi. *Note: It takes 5 sec.* (https://openwrt.org/toh/raspberry_pi_foundation/raspberry_pi)
![Screenshot 2022-05-20 20 30 35](https://user-images.githubusercontent.com/93491482/169657761-5d0e940e-fc5f-4579-b8de-944b32fbc0bc.png)
It's time to write this image into our Pi. So, First, We need to put my SD card to my PC and open the Pi Imager. Under Operating system click "CHOOSE OS" then select "Use custom". Then select the file you download from openWRT. Now click on "Storage" on pi Imager and select your sd card. Mine was SDHC 8GB and hit "Write" button. It will take less than 50 seconds. After It write is done, it automatically reject the SD card. Then put that sd card to your respbarry pi. 
![Screenshot 2022-05-20 20 33 20](https://user-images.githubusercontent.com/93491482/169658179-cbd943d3-4018-45a8-b465-5043956013e7.png)
Now, take your Ethernet wire, one side goes to Raspberry pi, and other side connect with your laptop or computer what ever it is. And connect the power in pi. It will boot up automatically. 

## Pi Setup
So, now we have to access our pi with new openWRT. So as my computer connected with raspbbery pi with ethernet by default my Pi has IP address which is 192.168.1.1. Now our goal is to be able to access this IP address so we can configure that. For that, we have to give our computer our interface and IP address in that subnet. IP address 192.168.1.10. 

For windows user
1. open your control panel and click on view n![Screenshot 2022-05-20 20 45 26](https://user-images.githubusercontent.com/93491482/169658736-9abd2c61-1bcd-4284-bd5d-fa3f4d168089.png)
etwork status and tasks. 
2. In Network 3, Click on Ethernet2. 
![Uploading Screenshot 2022-05-20 20.45.26.png…]()
Then click on properties > Internet Protocol Version 4 (TCP/IPV4). Then change from Obtain an IP address automatically to use the following IP address: 
![Screenshot 2022-05-21 11 42 59](https://user-images.githubusercontent.com/93491482/169659100-e3dfc596-3c05-455e-8551-70d6c8ce6dec.png)

For MAC user
1. Open system preferences > Network, the select from the ethernet from the left side and click on drop down menu for "Configure IPV4" and select manually then use the same as shows for windows. 

After that your raspberry pi is ready to access. For this is project I used both Windows and Mac. As it take a lots of time so, i switch between them. 

## Access Pi
Now we have to try to access this pi. we know the IP address and the username is "root". To access this we just need to open command prompt(for windows) or terminal(for mac and linux) then type 
```
ssh root@192.168.1.1
```
the type "yes"
and it will connect with OPENWRT. As there is no password setup, it won’t ask for password. This is insecure. We have to make a password now. type
```
passwd
```
Now input your new password and confirm it. If it successfully changed the password it will shows like that
```
passwd: password for root changed by root
```

Now, it's time to access pi. Type
 ``` 
 cd /etc/config
 ```
 ```
 ls
 ```
 you can see there is some awesome file. Like, dhcp, dropbear, firewall, luci, network, rpcd, system, ucitrack, uhttpd, wireless.
 All this files hold ip configuration of our raspberry pi router. We need to make some change in this file. But before make this file changes, we should and we must make a backup file, incase we mass it up. We will backup only three files which we are going to change. network, wireless, firewall
How to backup file? Follow this command one by one. 
```
cp firewall firewall.bk
cp wireless wireless.bk
cp network network.bk
```
Now check for it accually works or not. Do 
```
ls
```
Owal!!!!!! Now we can actually play with this file, even though we mess it up. 

## configure OpenWRT Network
So, as we go /etc/config now we have to edit some configuration for network, firewall and wireless files. To edit this we need a text editor. In there we are going to use vi text editor software. So type
```
vi network
```
Now we have to change something in here. First we need to change the IP address. In the "config interface 'lan'" go to option ipaddr and change the way you like,(Note: to edit in vim editor, first press "i" in your keyboard, then type whatever you want to) I'm using 10.97.97.1(make sure that is private). then create a new option under the "option ip6assign '60'" ----> "option force_link '1' ".
That means that is ip address is 10.97.97.1 and keep this config and save it whether he is up or not. 

Now we have to create a new inteface
Follow along with 
![Screen Shot 2022-05-21 at 12 42 53 PM](https://user-images.githubusercontent.com/93491482/169661644-7ab8d6f2-ecd3-408d-b70f-d53eced59d59.png)

Now after we done editing we need to exit vim editor, How can you we do?  
So save and exit the vim editor we need to use three things. 
1. Hit "Esc" in your keyboard
2. type ":wq"
3. Hit "Enter"

Now, it time to change our firewall.
```
vi firewall
```
Here, we are going to change "config zone 'wan' > option input REJECT to ACCEPT. (you vi same as before to insert and save and exit)
![Screen Shot 2022-05-21 at 1 07 32 PM](https://user-images.githubusercontent.com/93491482/169662002-6adb6f78-29a5-4850-b9e7-45ae08e1d23b.png)

Now exit the vi editor type
```
reboot
```
Now we are lose connection. Whatever we did before, i mean edit the file now we can actually use that. When it lose connection it will shows the previous ip address. But I change my ip as 10.97.97.1
So all we had to do now change our adapter back to our DHCP. Where in windows go to control panel and click the ethernet 2 same as before > properties > Internet Protocol Version 4 (TCP/IPv4) > obtain an IP address automatically. 
![Screenshot 2022-05-21 13 44 18](https://user-images.githubusercontent.com/93491482/169663340-1cf43dc2-f3b0-45bf-94d4-f60cc67b5f45.png)

Now type 
```
ipconfig
````
in windows and look the interface under Ethernet adapter Ehernet: 
Here it shows IPv4 Address: 10.97.97.(something)
which is connected with Default Gateway 10.97.97.1

Now it's time for check my private ip is working or not.
```
ssh root@10.97.97.1
```
It should ask for password. And type yes
![Screen Shot 2022-05-20 at 11 27 23 PM](https://user-images.githubusercontent.com/93491482/169663546-5ac39861-32bd-4b87-b213-d024be81e1c5.png)

#### Great, We manage our Raspberry Pi Router Securely.


## Connect OpenWrt to Wifi
Now, we have our new IP address on our raspberry pi, new subnet. Our next task is to get our USB Wifi adapter set up. If we just plug it in to our raspberry pi, nothing will acually happen. Before we plug in this usb wifi adapter, we need to download and install some driver. To download those driver our raspberry pi need internet access. Now our task is to get his internal wifi adapter to connect my home router. To get this access,
in the terminal type

```
vi /etc/config/wireless
```
We have to make one change to our radio0 interface, which is the default built-in on our raspberry pi. We have to make this change in order to make this work. 
![Screen Shot 2022-05-20 at 11 46 20 PM (2)](https://user-images.githubusercontent.com/93491482/169664254-d229c43f-86ba-4eba-8673-d96edb8d3995.png)
this should work now. You can go to your wifi search in your phone
![IMG_3970](https://user-images.githubusercontent.com/93491482/169665183-03db031c-79dc-44e7-aaf0-df5fe9ab7742.PNG)
