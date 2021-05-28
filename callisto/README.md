## Callisto apogee observation
Apogee observations will give the best setup for possible detection of OH spectral lines in the planet
atmosphere

Basic strategy: Observe Jovian moon Callisto at the closest orbit of Jupiter to Earth (perigee), but
when the moon is at it's farthest separation from the planet (apogee).


### Possible observation dates during orbit period
First off plot Jupiter-Earth orbit for 2021 to find dates around perigee    
[Jupiter_orbit_calculation.ipynb](https://github.com/rubyvanrooyen/observation_planning/blob/main/callisto/Jupiter_orbit_calculation.ipynb)    
Results seem to indicate late August to have closest approach.

Plot Callisto's orbit around Jupiter to identify apogee dates during    
[Callisto_orbit_calculation.ipynb](https://github.com/rubyvanrooyen/observation_planning/blob/main/callisto/Callisto_orbit_calculation.ipynb)    
Callisto orbits Jupiter every 17 days

Results from orbit simulations in notebooks indicate dates over August/September 2021.


### Setting up for solar body and planet moon observations
Using the simulation environment of AstroKAT that runs independent of MeerKAT live system

First evaluation is to find the rise and set times for Jupiter/Callisto given possible observation dates
```
astrokat-targets.py --body Jupiter --cat-path catalogues/ --text-only --lst --datetime '2021-08-01 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --text-only --lst --datetime '2021-08-15 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --text-only --lst --datetime '2021-08-31 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --text-only --lst --datetime '2021-09-01 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --text-only --lst --datetime '2021-09-15 00:00'
```
Rise and set times for the planet Jupiter over August and September
```
Rise Time (LST) Set time (LST)
17:11:43.33     3:04:07.82
17:03:40.73     2:58:50.57
16:54:07.20     2:52:29.63
16:53:32.85     2:52:06.71
16:46:17.69     2:47:15.64
```
Assuming `>20`[deg] horizon, a LST range of 18:00 - 03:00 observation time (pushing it down to 03:00
for the very northern pol cal)


Identify possible calibrator sources
```
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux pol --text-only --lst --datetime '2021-08-01 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux pol --text-only --lst --datetime '2021-08-15 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux pol --text-only --lst --datetime '2021-08-31 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux pol --text-only --lst --datetime '2021-09-01 00:00'
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux pol --text-only --lst --datetime '2021-09-15 00:00'
```
Selected calibrators, using closeby BPcal as Gaincal as well
```
Sources         Class                           RA              Decl
Jupiter         special
J0137+3309      radec polcal                    1:37:41.30      33:09:35.1
J0408-6545      radec fluxcal                   4:08:20.38      -65:45:09.1
J1939-6342      radec fluxcal                   19:39:25.03     -63:42:45.6
J2206-1835      radec gaincal bpcal             22:06:10.42     -18:35:38.7
```


Create a catalogue CSV file for planning and observation file generation
```
astrokat-targets.py --body Jupiter --cat-path catalogues/ --cal-tags gain bp flux --text-only --lst --datetime '2021-08-01 00:00' --outfile jupiter.csv
```
Edit file to reflect tags as indicated above and to manually add Jovian moon Callisto as special
target
```
> cat jupiter.csv
# Observation catalogue for proposal ID None
# PI: Ruby van Rooyen
# Contact details: ruby@ska.ac.za
J0137+3309 | 3C48, radec polcal, 1:37:41.30, 33:09:35.1, (50.0 50000.0 0.524 1.738 -0.6396 0.0498)
J0408-6545 | 0408-658, radec bpcal fluxcal, 4:08:20.38, -65:45:09.1, (145.0 18000.0 -0.979 3.366 -1.122 0.0861)
J1939-6342 | 1934-638, radec bpcal fluxcal, 19:39:25.03, -63:42:45.6, (408.0 8640.0 -30.77 26.49 -7.098 0.6053)
J2206-1835 | 2203-188, radec gaincal bpcal, 22:06:10.42, -18:35:38.7, (145.0 99000.0 2.185 -0.7118 0.1563 -0.0215)
Jupiter, special
Callisto, special
```
Note, Callisto is a `ephem.PlanetMoon` object and therefore does not have an explicit rise/set time or
separation angle calculation, these concepts do not exist, only separation from Jupiter.
Thus, Jupiter is used as proxy to obtain rise/set times as well as calibrators with separation angle
calculations.


View catalogue elevation plots to review calibrator selection and possible observation sequence
```
astrokat-targets.py --view jupiter.csv --datetime '2021-08-01 00:00'
astrokat-targets.py --view jupiter.csv --datetime '2021-09-01 00:00'
```
```
Observation Table for 2021/8/1 00:00:00 (UTC)
Times listed in UTC for target rise and set times
Target visible when above 20.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
Jupiter         special                         22:06:49.55     -12:49:42.2     19:03:31        04:54:39        159.33          Separation from Sun
Callisto        special                         22:06:54.20     -12:49:26.4     None            None             ***
J0137+3309      radec polcal                    1:37:41.30      33:09:35.1      01:33:20        05:33:11
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.1     22:38:04        13:18:55
J1939-6342      radec bpcal fluxcal             19:39:25.03     -63:42:45.6     14:23:22        04:42:16
J2206-1835      radec gaincal bpcal             22:06:10.42     -18:35:38.7     18:50:14        05:06:28
```


-fin-
