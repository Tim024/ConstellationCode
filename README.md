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

- `[WALKER]` being 'D' for delta and 'S' for Star. Delta will spread the (raan of the) planes evenly between 0-360 degrees, star between 0-180 degrees. It is possible to add a
  manual raan offset by adding a number after the walker: `[D or S]/[RAAN_OFFSET]`.
- `[ALTITUDE]` being the altitude in km. In case of elliptical orbits, the altitude is: `[APOGEE]/[PERIGEE]/[ARG_PERIAPSIS]`.
- `[INCLINATION]` being the inclination in degrees.
- `[PLANE PARAMS]` being the plane parameters, following the walker definition `[N_SATS]/[N_PLANES]/[F]` with `N_SATS` the total number of satellites, `N_PLANES` the total number
  of planes and `F` the relative spacing between planes. `N_SATS` should be divisible by `N_PLANES`. The offset between planes in degrees is `360*F/N_SATS`. The mean anomaly of the satellites is spread evenly amongst the satellites per plane. A fixed offset can be introduced by adding a number after the plane parameters: `[N_SATS]/[N_PLANES]/[F]/[ANOMALY_OFFSET]`.

This represents one shell. Constellations with multiple shells are defined by adding more shells separated by a `+`. As an example:

```
D:6400:45:189/9/2+D:11585/1215/270:63.4:57/7/1
```

represents a constellation with 2 shells. The first one is composed of 9 planes, 21 satellites per plane, 189 satellites in total. The altitude is 6400km and the inclination is 45
degrees.
The second one is composed of 8 planes, 7 satellites per plane, 56 satellites in total. The altitude is 1215km (south pole) and 11585km (north pole) and the inclination is 63.4
degrees.

In case the constellation has 1 plane, the walker can be omitted and the plane param is composed of only the number of satellites.

This definition is intended for orbital simulation.
It does not consider the initial epoch and therefore the position of satellites relative to earth may vary depending on how the parameters are interpreted.
As such, it's not suitable for representing GSO satellites.

## Examples

| Name                            | Operator               | Constellation                                  | Code              |
|---------------------------------|------------------------|------------------------------------------------|-------------------|
| Global Positioning System (GPS) | USSF                   | 24 in 6 planes at 20,180 km (55° MEO)          | D:20180:55:24/6/1 |
| Galileo                         | EUSPA, ESA             | 24 in 3 planes at 23,222 km (56° MEO)          | D:23222:56:24/3/1 |
| O3b                             | SES                    | 20 at 8,062 km, 0° (circular equatorial orbit) | 8062:0:20         |
| Iridium                         | Iridium Communications | 66 at 780 km, 86.4° (6 planes)                 | S:780:86.4:66/6/1 |

## Regex

Below is an example of a regex that can be used to parse the constellation code:

```
regex_split_multicode = "\+"
regex_code = "^(?:([DS/\d]*):)?([\d\./]+):([\w\.]+):([\d\./]+)$"
regex_walker = "^([DS])/?([\d\.]+)?$"
regex_altitude = "^([\d\.]+)(?:/([\d\.]+))?(?:/([\d\.]+))?$"
regex_plane = "^([\d\.]+)(?:/([\d\.]+))?(?:/([\d\.]+))?(?:/([\d\.]+))?$"
```

## Python

Based on the regex above, the following python code can be used to parse the constellation code:

```python
import re

example_codes = [
    "D:6400:45:189/9/2",
    "D:11585/1215/270.1:63.4:57/7/1",
    "D:6400:45:189/9/2+D:11585/1215/270:63.4:57/7/1",
    "D/45:1200:45:10/2/2/10",
    "8062.2:10:20",
]

regex_split_multicode = "\+"
regex_code = "^(?:([DS/\d]*):)?([\d\./]+):([\w\.]+):([\d\./]+)$"
regex_walker = "^([DS])/?([\d\.]+)?$"
regex_altitude = "^([\d\.]+)(?:/([\d\.]+))?(?:/([\d\.]+))?$"
regex_plane = "^([\d\.]+)(?:/([\d\.]+))?(?:/([\d\.]+))?(?:/([\d\.]+))?$"

for multicode in example_codes:
    print(multicode)
    codes = re.split(regex_split_multicode, multicode)
    for code in codes:
        items = re.findall(regex_code, code)[0]
        walker_params = items[0]
        altitude_params = items[1]
        inclination = items[2]
        plane_params = items[3]
        print(f"inclination: {inclination}")

        if walker_params != '':
            items_walker = re.findall(regex_walker, walker_params)[0]
            delta_or_star = items_walker[0]
            raan_offset = items_walker[1]
            print(f"delta_or_star: {delta_or_star}")
            print(f"raan_offset: {raan_offset}")

        items_altitude = re.findall(regex_altitude, altitude_params)[0]
        apogee = items_altitude[0]
        perigee = items_altitude[1]
        arg_periapsis = items_altitude[2]
        print(f"apogee: {apogee}")
        print(f"perigee: {perigee}")
        print(f"arg_periapsis: {arg_periapsis}")

        items_plane = re.findall(regex_plane, plane_params)[0]
        n_sats = items_plane[0]
        n_planes = items_plane[1]
        f = items_plane[2]
        nu_offset = items_plane[3]
        print(f"n_sats: {n_sats}")
        print(f"n_planes: {n_planes}")
        print(f"f: {f}")
        print(f"nu_offset: {nu_offset}")
```