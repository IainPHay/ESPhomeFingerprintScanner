# ESPhomeFingerprintScanner
Details on grow fingerprint scanner to control homeassistant entity ( in this case garage door)

Garage door controlled by an ESPhome on Wemos D1 with relay hat on it, rather than add the scanner to this a second WemosD1 was used as was more convenient for location but it could be done locally. In this case the fingerprint controller calls a homeassistant service (the garage door).

Project needs a grow fingerprint scanner:

aliexpress.com/item/33053655412.html?spm=a2g0o.productlist.0.0.381b7b2bb3yRCT&algo_pvid=0ccb058a-e722-4698-b1bf-c519ad9d609c&algo_exp_id=0ccb058a-e722-4698-b1bf-c519ad9d609c-0&pdp_ext_f={"sku_id":"10000014183447850"}&pdp_pi=-1;10.26;-1;-1@salePrice;GBP;search-mainSearch

an external waterproof box (any old socket, junction box etc) to mount it and a scanner cover :

aliexpress.com/item/4000260901035.html?spm=a2g0o.productlist.0.0.241272b8QFCB5F&algo_pvid=ddb8f30d-bb58-43e5-99ce-e15fd0936e3f&algo_exp_id=ddb8f30d-bb58-43e5-99ce-e15fd0936e3f-0&pdp_ext_f={"sku_id":"10000001058733147"}&pdp_pi=-1;0.73;-1;-1@salePrice;GBP;search-mainSearch

For convenience I use'd cat6 cable soldered direct to the sensor leads and to the WemosD1, cut a cable in half and your plugs are ready to plug in to a couple, makes it easy to take apart

I soldered cat6 cable onto the leads to run through wall and used the same on the wemos d1 with a coupler, it's a nice easy easy way to have disconnectable sensors.

Connection of grow sensor is easy, 3.3v line, ground, 5v, sensing pin for when finger on scanner and TX and RX.

![image](https://user-images.githubusercontent.com/25230544/156944397-8791e437-3671-465c-8656-f6b2e8b38d2f.png)


To add a fingerprint you call ESPHome: fingerprintgarage_enroll in this example and set the number of scans and id number and pop your finger or thumb on

In use when scanning a finger it calls the homeassistant service to open garage door
