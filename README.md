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

To add a fingerprint you call ESPHome: fingerprintgarage_enroll in this example and set the number of scans and id number and pop your finger or thumb on

The code for the scanner is:

api:
  services:
  - service: enroll
    variables:
      finger_id: int
      num_scans: int
    then:
      - fingerprint_grow.enroll:
          finger_id: !lambda 'return finger_id;'
          num_scans: !lambda 'return num_scans;'
  - service: cancel_enroll
    then:
      - fingerprint_grow.cancel_enroll:
  - service: delete
    variables:
      finger_id: int
    then:
      - fingerprint_grow.delete:
          finger_id: !lambda 'return finger_id;'
  - service: delete_all
    then:
      - fingerprint_grow.delete_all:
    
uart:
  tx_pin: D0 
  rx_pin: D1 
  baud_rate: 57600

fingerprint_grow:
  sensing_pin: D2 
  on_finger_scan_matched:
   # This bit toggles the cover via homeassistant, delay is needed
    - homeassistant.service:
        service: cover.toggle
        data:
          entity_id: cover.garage_door
    - delay: 1000ms
    
  
    - fingerprint_grow.aura_led_control:
        state: BREATHING
        speed: 200
        color: BLUE
        count: 1
# next section lets you define a user for a fingerprint number, you can have 200 prints, we use a couple per person, thumbs seem to work best on this scanner. Even have a setup for neighbour so he can pop in and borrow t
 Even have a setup for neighbour so he can pop in and borrow tools etc, we do this a lot, friendly bunch round here
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Authorized finger"
    - lambda: |-
        if (finger_id == 0) {
          id(fingerprint_username).publish_state("Iain");
        } else if (finger_id == 1) {
          id(fingerprint_username).publish_state("Iain");
        } else if (finger_id == 2) {
          id(fingerprint_username).publish_state("Wife");
        } else if (finger_id == 3) {
          id(fingerprint_username).publish_state("Wife");
        } else if (finger_id == 4) {
          id(fingerprint_username).publish_state("Son");
        } else if (finger_id == 5) {
          id(fingerprint_username).publish_state("Son");
        } else if (finger_id == 6) {
          id(fingerprint_username).publish_state("Neighbour");
        } else {
          id(fingerprint_username).publish_state("Unknown");
        };
    - delay: 10s
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Scan your finger"
        
  on_finger_scan_unmatched:
    - fingerprint_grow.aura_led_control:
        state: FLASHING
        speed: 25
        color: RED
        count: 2
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Unauthorized finger"
    - delay: 3s
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Scan your finger"
  on_enrollment_scan:
    - fingerprint_grow.aura_led_control:
        state: FLASHING
        speed: 25
        color: BLUE
        count: 2
    - fingerprint_grow.aura_led_control:
        state: ALWAYS_ON
        speed: 0
        color: PURPLE
        count: 0
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Finger scanned"
  on_enrollment_done:
    - fingerprint_grow.aura_led_control:
        state: BREATHING
        speed: 100
        color: BLUE
        count: 2
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Enrolled fingerprint"
  on_enrollment_failed:
    - fingerprint_grow.aura_led_control:
        state: FLASHING
        speed: 25
        color: RED
        count: 4
    - text_sensor.template.publish:
        id: fingerprint_state
        state: "Failed to enroll fingerprint"
    
sensor:
  - platform: fingerprint_grow
    fingerprint_count:
      name: "Garage Fingerprint Count"
    last_finger_id:
      name: "Garage Fingerprint Last Finger ID"
    last_confidence:
      name: "Garage Fingerprint Last Confidence"
    status:
      name: "Garage Fingerprint Status"
    capacity:
      name: "Garage Fingerprint Capacity"
    security_level:
      name: "Garage Fingerprint Security Level"
 
So this gives access and gives a log of who has used it. Planning to add some automation to control lights, open internal door etc.
    
Iain Hay
16:48
To add a fingerprint you call ESPHome: fingerprintgarage_enroll in this example and set the number of scans and id number and pop your finger or thumb on
3 MAR
MarkWattTech
17:58
Awesome thank you very much. Is yours mounted ourtside?
*outside
I will be adding it to my list of ever expanding projects
YESTERDAY
Iain Hay
10:39
Yep outside in a waterproof box, I'll grab some photos.
TODAY
MarkWattTech
08:41
