# how-to

1. Download and process data,

```bash
file=malaysia-singapore-brunei-latest
# ~174MB
wget http://download.geofabrik.de/asia/${file}.osm.pbf
docker run -t -v "${PWD}:/data" osrm/osrm-backend osrm-extract -p /opt/car.lua /data/${file}.osm.pbf
docker run -t -v "${PWD}:/data" osrm/osrm-backend osrm-partition /data/${file}.osrm
docker run -t -v "${PWD}:/data" osrm/osrm-backend osrm-customize /data/${file}.osrm
```

2. Serve backend,

```bash
file=malaysia-singapore-brunei-latest
docker run -d -t -i -p 5000:5000 -v "${PWD}:/data" osrm/osrm-backend osrm-routed --algorithm mld /data/${file}.osrm
```

3. Request some examples,

```bash
time curl 'https://osrm.malaysiaai.ml/route/v1/driving/100.46722412109374,6.058623804918194;100.51211357116699,6.01423932199451?overview=false'
```

```text
{"code":"Ok","routes":[{"legs":[{"steps":[],"distance":10209,"duration":1050.8,"summary":"","weight":1074.3}],"distance":10209,"duration":1050.8,"weight_name":"routability","weight":1074.3}],"waypoints":[{"hint":"3rgigOO4IoAAAAAAEgAAAAAAAAB5AQAAAAAAAE8QTEEAAAAAmLGCQwAAAAASAAAAAAAAAHkBAAB3DwAAkQf9BUR5XAAYAv0FgHJcAAAA3wWyG2KR","distance":246.443801,"name":"","location":[100.468625,6.060356]},{"hint":"Ha0igB-tIoADAAAADQAAAAkBAAASAAAAOgJxQKlNX0ErjpJD9WaeQQMAAAANAAAACQEAABIAAAB3DwAAa7H9BU3EWwBysf0FH8VbAAsA_xCyG2KR","distance":23.234645,"name":"","location":[100.512107,6.014029]}]}
real	0m0.495s
user	0m0.024s
sys	0m0.011s
```

Entire documentation at http://project-osrm.org/docs/v5.5.1/api/#general-options

4. Run as a service,

```
sudo cp osrm.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable osrm.service 
sudo systemctl start osrm.service
```
