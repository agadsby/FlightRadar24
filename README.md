# FlightRadar24
Display a simple radar plot of your Raspberry Pi's Flightradar24 detector's data.

This program shows the output being seen by the local FR24 receiver. It was created to visualise, in real-time, the data obtained by the FR24 feed. 

<img width="500" alt="sampleplot" src="https://github.com/user-attachments/assets/6342e855-e063-47d9-9ce3-e10f02dc368c" />

NOTE: Only aircraft with transponders providing lat/lon information will be shown on the radar plot.

Each aircraft is shown at its current position with a line showing the vector the aircraft will travel at its current speed and heading in the next minute. The colour of the line represents the altitude using standard FR24 [colours](https://support.fr24.com/support/solutions/articles/3000115027-why-does-the-aircraft-s-trail-change-colour-) The "tell" next to the aircraft gives its callsign and altitude.

Clicking on the current location of a displayed arcraft will open a new window showing details of that flight using the offical FR24 site.

A table showing more information about all of the aircraft that are seen by the receiver AND are reporting a lat/long position.

The display updates every 5 seconds.

## Modifying the Plot.
To change the range of the displayed plot simply update the Max Range field below the plot - the unit is Nautical Miles.

To filter the display based on reported altitude simply change the Min and Max Alt fields - the unit is Feet.

## Installation on a raspberry Pi
After you have installed the FR24 software on your local Raspberry Pi, place the downloaded **show.html** and **help.html** files into **/var/www/html/**. Make sure that these are readable by the lighthttpd process.

Modify **/etc/lighthttpd/lighthttpd.conf**:

At the end of the file add:
```
##### redirect flights.json to FR24 port ####
server.modules += (
		"mod_proxy",
	) 

proxy.server = ( "flights.json" =>
                       ( (
                           "host" => "localhost",
                           "port" => 8754
                         ) )
                     )
```
Restart the service using:

`sudo /etc/init.d/lighttpd restart`

This change allows the Javascript AJAX request to get the flight information passed to the correct port on the FR24 server.

By default the program will infer the location of the receiver by assuming it is in the centre of the received aircraft - this is almost certainly INCORRECT but it allows you to get a picture of the aircraft that are visible.

To correctly locate the receiver create a file called **location.txt** in the same directory as show.html. This file should contain a BASE location (latitude, longitude) for your receiver. 

**NOTE: longitude is negative for locations WEST of Greenwich** 

The example below is for a receiver located West of London at Latitude 51.5281 \(Northern hemisphere\)and Longitude -0.9048 \(West of the Greenwich meridian\). In addition it also includes some local airports that are to be displayed on the plot (LTN being Luton and LHR being Heathrow)
```
# name, lat N+, lon W- as per FR24
# first location should be the BASE location for the receiver.
BASE,51.5281,-0.9048
# other useful places to show on radar plot
LTN,51.8864,-0.2413
LHR,51.4680,-0.4551
```

Once loaded the displayed plot should be centred on your location with the other useful places also shown, if in range.

At this point use your browser to access your Raspberry Pi using `show.html` as the url.
