---
id: "release_notes_content"
lang: "en"
title: "0.5.0 Release Notes"
label1: "Release Notes"
---
# 0.5.0 Release Notes


## Overview

ZILLIZ Analysis 0.5.0, released on November 30, 2019, has the following improvements compared with the 0.4.0 version:

- Added adaptation layer for the storage engine with support for kudu.
- Added memory object management based on Apache Arrow.
- Added GPU-accelerated mechanism to import CSV files.
- Added new chart types.
- Improved the exception handling mechanism and system stability.
- Improved the performance of computation and rendering operators.

## Infini visualized analytics interface

### System configuration and components

- Added support for importing/exporting/deleting the configuration file.
- Added the favicon system.
- Updated React to 16.10.

### Dashboards and charts

- Optimized the performance of multi-chart rendering.
- Added statistics of selected and unselected data points.
- Added bubble chart.
- Added histogram.
- Added animation effect for data changes for the pie chart.
- Added animation effect for data changes for the line chart.
- Added animation effect for data changes for the bar chart.
- Added new rendering styles for the heat chart.
- Added feature for the point map to display all information for a selected point.

### Chart editor

- Added 4 color palettes for the point map with measurements.
- Added customizable latitude.
- Added customizable measurements.
- Added new mechanism for text columns in line charts to avoid displaying too many lines. The line chart displays 10 columns by default if the number of text columns is more than 10.


## MegaWise data analytics engine

### System configuration

- Added unified configuration files for customized system configuration.

### Data interface

- Added adaptation for kudu.
- Added GPU-accelerated mechanism to import CSV files.

### SQL execution engine

- Improved support for const queries.
- Improved exception handling mechanism, including:
    - The SQL engine stops queries and recycles resources once the computation runs into an exception.
    - Improved functionality bounds checking by returning messages for unsupported SQL phrases.
- Improved the algorithm of hash join to optimize GPU memory usage.
- Added support for printing the codegen plan.
- Added support for extracting quarter.

### Memory management

- Added memory object management based on Apache Arrow.

## Picasso graphics rendering engine

- Added data rendering types, including:

    - layer\_weighted\_pointsize\_circles\_2d: Weighted 2-dimensional scatter plot with variable size and invariable color.

- Optimized the rendering speed of the heat chart.

## Bug fixes

- 1000419         Fixed the bug that the sort node threw exceptions.
- 1000417         Fixed the bug that exceptions occurred when executing queries for 0 rows of data.
- 1000414         Fixed the bug that the points disappeared when using a polygon to select data from a point map.
- 1000411         Fixed the bug that the message ERROR: rc=stoll appeared when performing avg on strings.
- 1000405         Fixed the bug that after restarting docker, the table still existed but did not include any data.
- 1000399         Fixed the bug that strings could not be compared using the equal(=) sign.
- 1000395         Fixed the bug that port error occurred when megawise\_server stopped because of the wrong process of grp server shutdown.
- 1000384         Fixed the bug that string was incorrectly processed and caused substring error when generating the result.
- 1000379         Fixed the bug that multifragment engine error occurred because meta execution was not based on data type.
- 1000368         Fixed the bug that cosmo copy from csv did not read consistent number of lines when there is a header.
- 1000366         Fixed the bug that the engine wrongly processed boolean expr and caused boolean error.
- 1000365         Fixed the bug that plot\_scatter\_2d returned an error when the subquery returns a result set with 0 elements.
- 1000337         Fixed the bug that the message \d error appeared because of errors in the pg compilation process.
- 1000335         Fixed the bug that port error occurred when chewie client exited because of wrong links in arrow\_cuda.a.
- 1000334         Fixed the bug that caused temp table error.
- 1000330         Fixed the bug that MegaWise Docker could not process the stop command.
- 1000328         Fixed the bug that when closing MegaWise Docker, /tmp/.s.PGSQL.5432 was not removed.
- 1000327         Fixed the bug that pg\_ctl could not launch in Ubuntu 18.04.
- 1000326         Fixed the bug that information was lost when cosmo load\_from csv interface invoked grpc for the second time.
- 1000298         Fixed the bug that GIS tests failed because of inconsistent graph mechanisms.
- 1000293         Fixed the bug that gtest compilation errors caused compilation errors for unit tests.
- 1000167         Fixed the bug that GIS was unstable because of x-server dependency.
- 1000336         Fixed the bug that distance\_in\_meters could not run in some conditions and threw exceptions.
- 1000453         Fixed the bug that when a polygon was selected, an error occurred when clicking to select the polygon.
- 1000454         Fixed the bug that tooltip was not accurately positioned when multiple lines in a line chart were not consistent in size.
- 1000455         Fixed the bug that color was not correctly displayed when selecting points in a a point map.
- 1000456         Fixed the bug that the SQL parser did not generate a correct SQL under phrase multiple OR conditions .
- 1000456         Fixed the bug that numerics in the number chart could not automatically resize based on the chart size.
- 1000415         Optimized the execution process of specific SQLs. Fixed the bug that specific SQLs in the heat chart had a long graphics rendering time.
- 1000425         Fixed the bug that selections in the color palette were not consistent with actual colors for the point map.
- 1000410         Fixed the bug that in GeoHeatMap, when AVG(passenger\_count) was selected as MEASURE, the graphics was a rectangle.
- 1000323         Fixed the bug that when the result set was empty or contained a const expression, GPU memory leak occurred.
- 1000451         Fixed the bug that scheduling error occurred for HASH JOIN when using multiple GPUs.
- 1000452         Fixed the bug that task creation error occurred for the hash node when processing big data.
- 1000443         Fixed the bug that when the intermediate result had 0 rows, the hash node failed to create tasks.
