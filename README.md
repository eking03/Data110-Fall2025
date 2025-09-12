---
title: "LiDAR"
author: "Emma King"
date: 08/10/25
---


# Light Detection and Ranging

![lidar](https://www.energy.virginia.gov/geology/images/LiDAR_illustration.png)


## Introduction

LiDAR, which stands for *Light Detection and Ranging*, is a remote sensing technology that uses laser light to measure distances with high precision. By emitting rapid pulses of laser light and calculating the time it takes for each pulse to bounce back after hitting an object or surface, LiDAR systems can create detailed, 3D maps of environments. 


## Applications

LiDAR is useful in multiple fields, including, but not limited to:
- geography
- archaeology
- forestry
- environmental monitoring


## Data Format

The LiDAR laser pulse records more than just height, it also detects different properties about the terrain it reflects off of. The pulses have numbered classifications describing the surfaces.

1. Unassigned
2. Ground
3. Low vegetation
4. Medium vegetation
5. High vegetation
6. Building
7. Noise
8. Model key
9. Water
10. Rail
11. Road surface
12. Overlap
13. Wire - guard
14. Wire - conductor
15. Transmission tower
16. Wire - connector
17. Bridge deck
18. High noise

Some other attributes recorded by the LiDAR sensor are listed in the following table. 

| Attribute          | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| Position           | XYZ coordinates                                                             |
| GPS Time           | Time recorded at the moment the pulse was emitted from the sensor           |
| Intensity          | Strength of the pulse returned to the sensor                                |
| Return Number      | Order by which the pulse returned to the sensor                             |
| Number of Returns  | Total number of times the pulse returned to sensor                          |
| Edge of Flightline | Boolean describing whether the pulse was measured at the edge of scan line  |
| Classification     | Numeric value categorizing the surface the pulse was reflected off of       |
| Scan Angle         | Angle from the aircraft the pulse was emitted                               |

All of this data from the LiDAR sensor is stored in a LAS file. This file contains details of a 3D point cloud with millions of points. A **LAZ** file is condensed version of this. There are several strategies for minimizing the file size to allow for faster processing. The following is a list of details that may be excluded to reduce file size. 

[ ] keep first return pulse points only
[ ] keep last return pulse points only
[ ] remove points with an intensity measured beyond a certain threshold
[ ] remove points above or below a certain Z threshold
[ ] keep only points classified as ground
[ ] exclude points classified as noise


## Using LAS Files

LAS files can be accessed using several different softwares. The following code chunk is a fragment from a project I worked on, where a pedestrian bridge was scheduled to be built[^1]. This utilizes a package called `lidR` which helps visualize 3D point clouds in R. The data. can be found at the following site

```{r}

# https://montgomeryplanning.org/tools/gis-and-mapping/elevation-data/
# https://data.imap.maryland.gov/datasets/120913815e11405c9c8996b63a65087d_7/explore

las.twin <- "C:/Users/jaria/Downloads/216NW06/216NW06.laz"
las.twin <- lidR::readLAS(las.twin)

metro.stop <- st_read("C:/Users/jaria/Downloads/Maryland_Transit_-_WMATA_Metro_Stops/Maryland_Transit_-_WMATA_Metro_Stops.shp")
metro.stop <- metro.stop[metro.stop$NAME == "Twinbrook",]

twinbuff <- st_buffer(metro.stop, dist = units::set_units(0.25, "miles"))
twinbuff <- st_transform(twinbuff, st_crs(las.twin))

las.buff <- lidR::clip_roi(las.twin, twinbuff)
plot(las.buff)

```

[^1]: The LAS files are sourced from a local government website that has high quality LiDAR data for Montgomery County that is not available from USGS like it is for most places in the USA.


## Future of LiDAR

> A huge Maya city has been discovered centuries after it disappeared under jungle canopy in Mexico.
> Archaeologists found pyramids, sports fields, causeways connecting districts and amphitheatres in the southeastern state of Campeche.
> They uncovered the hidden complex - which they have called Valeriana - using Lidar, a type of laser survey that maps structures buried under vegetation.

This [excerpt](https://www.bbc.com/news/articles/crmznzkly3go) by Georgina Rannard in an article written for **BBC** highlight one way that LiDAR has helped us understand the shape of the world around us. 
Unlike traditional imaging methods, LiDAR works effectively in low-light conditions and can penetrate through vegetation, making it especially valuable for mapping terrain and detecting hidden structures. 


