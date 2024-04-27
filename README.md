# Optimizing Traffic Light Times with PSO
---
Utilizing SUMO, along with PSO, to optimize traffic light times and help enhance the flow of traffic along Cumberland Ave in Knoxville.

### Setup
---
- Make sure you have [SUMO](https://sumo.dlr.de/docs/Installing/index.html#macos) installed and setup.
- If you are wanting to use [Open Street Map](https://www.openstreetmap.org/#map=19/35.95678/-83.94020&layers=T) to export a specific roadway for the simulation, you will need to install and setup [JOSM](https://josm.openstreetmap.de).
    - JOSM is really only needed if you want to edit the portion of the map you exported

### How to Run
---
- In order to generate all of the files needed to run the simulation, you will need to execute the following: 

    1. Convert the OSM file from OpenStreetMap to XML & create the network
        `netconvert --osm-files filename.osm --output-file filename.net.xml --geometry.remove --junctions.join --tls.guess-signals --tls.join`

    2. Next, take the newly created network XML file and create random trips. This will create a specified number of trips within the simulation. It will define the starting and ending point for each vehicle.
    `python3 path/to/sumo/tools/dir/randomTrips.py -n networkfile.net.xml -e num_of_trips -o tripfile.trips.xml`
    _NOTE: You will need to make sure SUMO is setup correctly in order to do this. It requires you to run one of the tool scripts that install with it_

    3. Then, generate the route file. This will generate the valid routes for each vehicle to reach their destination from their corresponding starting point.
    `duarouter -n networkfile.net.xml --route-files tripfile.trips.xml -o routefile.rou.xml --ignore-errors`
    
    4. Lastly, you will then need to create a SUMO config (CFG) file. This will detail to the SUMO simulator what network file to use, along with what route file to use. You can also specify the begin and end time values, along with other configurations. An example is included in the SUMO_files directory.
    
- Next, before running the simulation, make sure XQuartz is running first. SUMO should of had you install this during the setup. It needs to be running in order for the SUMO GUI to work. 
- Finally, you can run the SUMO GUI using the `sumo-gui` command on the command line. Within the GUI, you can then load the SUMO CFG file and run the simulation. 

### Collecting Data & Interacting with the Results
---
To interact with the SUMO simulation, you can use the `traci` Python library. This will allow you to interact with and easily change values within the network file, as well as get certain information such as *arrival_time*, *time_spent_waiting*, and others for all vehicles in the simulation.

Currently, within the trips and route file, I have setup a vehicle to travel from one end of Cumberland Ave to the other end. It's vehicle ID is 10. We can start with gathering data for this one.

- One thing to note. Within the network file, where the traffic light times and configurations are defined (`tlLogic` tags), you can see the different light phases and times. With how SUMO defines these, we become a little limited in how we can set times for different light phases. As such, green light and yellow light times are free to be whatever we want and whatever PSO wants. But, red light times will be equal to green + yellow. This kinda sucks as we can't just globally define a specific time for each phase. But, we can still optimize around this and should be able to find some interesting results. 

### Potential Future Additions
---
If we have time, we could add back in sidewalks into the network and add in pedestrians / crosswalks to make the simulation a little more accurate. We could also maybe add in more traffic lights and the rest of UTK's campus.


### Experimental Parameters
---
- Defaults
    - num_particles (60)
    - inertia: 0.6
    - cognition: 1.6
    - Social: 1.6

1. Total waiting time runs: 
    - One run with all defaults
    - For different particle values
    - For different inertia values
    - for different social & cognition 

2. Total travel time runs: 
    - One run with all defaults
    - For different particle values
    - For different inertia values
    - for different social & cognition 
