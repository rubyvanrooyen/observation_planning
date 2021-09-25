ToO and filler observations of comet 67P/Churyumov-Gerasimenko    
Project ID assigned: `SSV-20210701-SA-01`

Comet orbits are defined in terms of XEphem heliocentric orbital parameters.    
These parameters are available on the `Ephemeris from Minor Planet & Comet Ephemeris Service` webpage    
See the
[67P_orbit_from_XEphem_ephemeris.ipynb](https://github.com/rubyvanrooyen/observation_planning/blob/main/comet67P/67P_orbit_from_XEphem_ephemeris.ipynb)
notebook shows the orbital parameters and calculation for the comet 67P.
```
67P/Churyumov-Gerasimenko, xephem, 67P/Churyumov-Gerasimenko~e~3.8713~36.3348~22.1246~3.457120~0.1533319~0.649811~341.5921~07/05.0/2021~2000~g 11.0~4.0
```


## Observation planning steps

* The comet heliocentric orbit means the target will move across the sky against the stellar background
over time.
Thus, evaluation/updates of calibrators over time has to be done sporadically.    
Given the comet XEphem coordinates and observation date, `katpoint`'s PyEphem implementation can be
used to calculate the elevation and close by potential MeerKAT calibrators.
Example calculations for generating a CSV catalogue to use with the engineering `image.py` observation script with SB instructions can be found in the
[67P_calibrator_selection.ipynb](https://github.com/rubyvanrooyen/observation_planning/blob/main/comet67P/67P_calibrator_selection.ipynb)
notebook.

* Currently `image.py` engineering script is used for observations.
Or after updates to `AstroKAT` a manual YAML observation file can be used.
Use the
[67P_katpoint_planning.ipynb](https://github.com/rubyvanrooyen/observation_planning/blob/main/comet67P/67P_katpoint_planning.ipynb)
notebook to plot elevation of observation targets, as well as skeleton SB instructions.
Update the sources list with the calibrators from the calibrator selection notebook.
This notebook will generate an updated CSV catalogue `comet67P_observe.csv`, that has to be downloaded.

* Commit observation catalogues and files to
[`katsdpcatalogues`](https://github.com/ska-sa/katsdpcatalogues)
repo and update SB skeleton.   
Update `desired_start_time` to appropriate observation time scheduled   
Submit SB instructions and elevation plots to AOD for scheduling.
```
obs.sb.new(owner="ruby")
obs.sb.type=katuilib.ScheduleBlockTypes.OBSERVATION
obs.sb.description="Cometary OH: 67P narrow band line filler observation"
obs.sb.antenna_spec="available"
obs.sb.controlled_resources_spec="cbf,sdp"
obs.sb.instruction_set="run-obs-script /home/kat/katsdpscripts/observation/image.py /home/kat/katsdpcatalogues/comet67P_observe.csv -t 900 -g 90 -b 300 -i 3600 -m 5400"
obs.sb.proposal_id="SSV-20210701-SA-01"
obs.sb.horizon=15.0
obs.sb.desired_start_time= '2021-09-23 23:00'
obs.sb.to_defined()
obs.sb.to_approved()
obs.sb.unload()
```

* Test observation by simulating on `devcomm`
```
ssh kat@monctl.devcomm.camlab.kat.ac.za
cd katsdpcatalogues
git pull
```

View GUI and schedule observation   
http://monctl.devcomm.camlab.kat.ac.za/katgui/login   
and use the ipython interface to create the SB and do a dry-run (verification) to simulate the
observation.

-fin-
