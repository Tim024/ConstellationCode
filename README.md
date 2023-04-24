# Constellation Code

**This constellation code attempts to define a format to define complex and large constellations in a concise way.**

TLEs (https://en.wikipedia.org/wiki/Two-line_element_set) represent the true position of the satellite.
However, using TLEs does not scale well for analyzing or simulating mega-LEO constellations.
The walker (https://en.wikipedia.org/wiki/Satellite_constellation) definition of constellations works well for circular orbits.
We extend this definition in a more generic way.

The format is as follows:
```
[WALKER]:[ALTITUDE]:[INCLINATION]:[PLANE PARAMS]
```
where:
- `[WALKER]` being 'D' for delta and 'S' for Star. Delta will spread the (raan of the) planes evenly between 0-360 degrees, star between 0-180 degrees. It is possible to add a manual raan offset by adding a number after the walker: `[D or S]/[RAAN_OFFSET]`.
- `[ALTITUDE]` being the altitude in km. In case of elliptical orbits, the altitude is: `[APOGEE]/[PERIGEE]/[ARG_PERIAPSIS]`.
- `[INCLINATION]` being the inclination in degrees.
- `[PLANE PARAMS]` being the plane parameters, following the walker definition `[N_SATS]/[N_PLANES]/[F]` with `N_SATS` the total number of satellites, `N_PLANES` the total number of planes and `F` the relative spacing between planes. `N_SATS` should be divisible by `N_PLANES`. The offset between planes in degrees is `360*F/N_SATS`. 


This represents one shell. Constellations with multiple shells are defined by adding more shells separated by a `+`. As an example:
```
D:6400:45:189/9/2+D:11585/1215/270:63.4:57/7/1
```
represents a constellation with 2 shells. The first one is composed of 9 planes, 21 satellites per plane, 189 satellites in total. The altitude is 6400km and the inclination is 45 degrees.
The second one is composed of 8 planes, 7 satellites per plane, 56 satellites in total. The altitude is 1215km (south pole) and 11585km (north pole) and the inclination is 63.4 degrees.

In case the constellation has 1 plane, the walker can be omitted and the plane param is composed of only the number of satellites.

This definition is intended for orbital simulation.
It does not consider the initial epoch and therefore the position of satellites relative to earth may vary depending on how the parameters are interpreted.
As such, it's not suitable for representing GSO satellites.

## Examples

| Name                            | Operator   | Constellation                                  | Code              |
|---------------------------------|------------|------------------------------------------------|-------------------|
| Global Positioning System (GPS) | USSF       | 24 in 6 planes at 20,180 km (55° MEO)          | D:20180:55:24/6/1 |
| Galileo                         | EUSPA, ESA | 24 in 3 planes at 23,222 km (56° MEO)          | D:23222:56:24/3/1 |
| O3b                             | SES        | 20 at 8,062 km, 0° (circular equatorial orbit) | 8062:0:20         |
| Iridium | Iridium Communications| 66 at 780 km, 86.4° (6 planes)                 | S:780:86.4:66/6/1 |

