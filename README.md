
# IAQ-Controller

Built with Node Red for a Raspberry Pi with a 16-channel digital input board and a 16-channel relay board from Sequent Microsystems.

Controller uses inputs from humidistats, occupancy sensors, IAQ sensors & manually initiated countdown timers to control ERV & dehumidifier connected to common supply duct via 3 dampers. ERV exhaust is ducted from all zones including showers, bathrooms, laundry rooms. System can control up to 8 exhaust zones using American Aldes ZRT or ZRT-2 terminals (a constant pressure damper and bypass exhaust intake may be needed if the ZRT terminal without the integrated CAR3 constant airflow regulating damper is used.) ZRT-2 is strongly recommened.

Products used in overall IAQ system:

https://www.santa-fe-products.com/product/ultra205-dehumidifier/ (I used the Ultra-Aire SD12, which is discontinued)

https://www.supplyhouse.com/Fantech-ATMO150E-ATMO-Series-Fresh-Air-Appliance-Energy-Recovery-Ventilator-6-Side-Ports-161-CFM
    (similar to my Clean Comfort ERV from Daikin)
    
https://www.amazon.com/gp/product/B07TD42S27/?th=1

https://sequentmicrosystems.com/products/sixteen-relays-8-layer-stackable-hat-for-raspberry-pi

https://sequentmicrosystems.com/products/16-universal-inputs-card-for-raspberry-pi

https://www.trutechtools.com/haven-iaq-central-air-monitor.html

https://www.supplyhouse.com/Honeywell-Home-H6062A1000-HumidiPRO-Digital-Humidistat-Dehumidistat-Humidity-Control (one in each exhaust zone, one in main living area)
https://www.amazon.com/gp/product/B0B4MVHRK5?th=1 one in each toilet area

https://www.supplyhouse.com/Aprilaire-6508-8-Round-Ventilation-Damper-Normally-Closed-Power-Open (one in ERV supply connecting to dehum return, balanced to maintain same static pressure on supply side of ERV supply fan as when it's supplying the duct system)

https://www.supplyhouse.com/Honeywell-Home-ARD8TZ-8-Round-Automatic-TrueZONE-Damper for ERV supply to duct system

https://www.supplyhouse.com/Honeywell-Home-ARD10TZ-10-Round-Automatic-TrueZONE-Damper for dehum supply to duct

ERV removes exhaust from up to 8 zones. Each zone has sensors such as humidistats and/or occupancy sensors, and has a zone damper integrated into the terminal. The zone damper includes the CAR3 airflow regulating damper, which provides for a small volume of air to be exhausted when the zone is not calling for exhaust and the main damper is closed, but the ERV is running on demand for ventilation or for exhuasting another zone or zones. If a zone calls for exhaust, the ERV starts, and the main damper in the ZRT opens. The terminal ceases to regulate/restrict air volume exhausted from that zone. The ERV will continue to run and the zone damper will stay open for 5 minutes after the sensor opens. 

ERV exhausts about 180 CFM from 7 zones, and supplies about the same volume of outdoor air. ERV supplies outdoor air one of two ways: through a dedicated supply duct system that it shares with a whole-house dehumidifier, or to the return air side of the dehumidifier if the dehum is also running. Two dampers control whether the ERV discharges to the supply duct system (damper 1) or to the dehum return plenum (damper 2). When supplying air to the return side of the dehum, a manual volume damper is adjusted to maintain the same supply side static pressure on the ERV supply blower as when the ERV is supplying air via the common duct system.

Dehumidifer only runs during demand for dehumidification from the main living area of the home. It moves about 400 cfm from a dedicated return, but if the ERV is also running, it mixes ~160 cfm of outdoor air in its return plenum with ~240 cfm of recirc air from the return. A damper on the dehum supply (damper 3) closes when dehum is off, preventing supply air from the ERV from flowing back through the dehum. During a call for dehumidification alone, dampers 1 and 2 are closed, isolating the ERV from both the return and supply of the dehumidifier.

During an exhaust or ventilation call only, the ERV runs and the dehumidifier remains off. Damper 1 is open, dampers 2 and 3 are closed.

If a call for dehum starts during a call for exhaust and/or ventilation, the ERV shuts down. A 30-second time delay elapses before the ERV and dehum start, giving the 3 dampers that control supply air from the ERV and dehum time to reposition for this mode. Damper 1 closes, dampers 2 and 3 open.

Similarly, if the dehum is running alone and a call for exhaust or ventilation starts, the dehum will shut down for 30 seconds, during which damper 1 remains closed, dampers 2 opens, and damper 3 remains open.

The controller monitors ERV runtime for minimum ventilation requirements. The MinVentMin variable is set to 15, which means that if exhaust and demand ventilation is called for less than 15 minutes out of each 1 hour period, the ERV will run for 15 minutes. 

