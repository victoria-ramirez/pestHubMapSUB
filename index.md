# pestHubMap

<!-- badges: start -->
<!-- badges: end -->

## Description

A framework and [research
compendium](https://ieco-lab.github.io/pestHubMap/index.html) to build
applications that map “hubs” at high risk of transporting invasive pests
among properties. The framework and code can be easily adapted to build
applications for any pest, and we provide our data and code in the
context of the spotted lanternfly (SLF; *Lycorma delicatula*).

-   *Why ‘pestHubMap’?* pest == invasive species + Hub == properties
    that facilitate invasive species spread + Map == interactive mapping
    applications
-   *Why build pestHubMaps?* Our maps are designed to provide agencies
    with information to find and optimally allocate resources to control
    invasive species and control spread.
-   *Why slf?* SLF is a rapidly spreading pest that hitchhikes on a wide
    range of goods and transportation infrastructure. SLF feeds
    on &gt;150 plant hosts and threatens billions of dollars of
    agricultural loss.

## Usage

To understand why this framework is useful and to build your own
applications:

1.  Read the [paper](LINK)
2.  Explore the [research
    compendium](https://ieco-lab.github.io/pestHubMap/index.html)
    <https://ieco-lab.github.io/pestHubMap/index.html>

## The Framework

![Our seven step pestHubMap framework:](figures/fig1_framework.png)

**Step 1. Identify hub types and other spatial data to map**

The first step is to create a keyword list of property types that are
probable hubs based on how a pest is spread by humans. We suggest
collaborating directly with stakeholders, experts, and agencies to
create this list. We provide a default list in the code repository and
suggest that users revise the default hub types as needed for their pest
species. Depending on the species and stakeholders who will use the
application, other spatial data to include are geopolitical outlines,
sampling grids, pest density estimates, and environmental suitability or
establishment risk. All data are overlaid in the application such that
field surveyors can easily find hub locations within larger boundaries
of high establishment risk. These different spatial data act as
different layers on each application; layers can be viewed on top of
each other or turned off to view one at a time (see Step 5).

**Step 2. Obtain hub location data**

The second step is to obtain location data (names, addresses, and
coordinates) for each of the hubs identified in Step 1. We developed
code using the Python programming language (python.org) , which we
executed in Google Colaboratory, to query the hub type keywords in an
online point of interest repository to scrape hub locations data. The
user enters a keyword (e.g., “amusement+park”) and region (e.g.,
“bucks+county+pa”). Depending on the size of the map and desired
resolution, users may choose to search by state, county, or city. The
code currently queries yellowpages.com, which is free to scrape, but by
using this repository the data cleaning step (Step 3) requires
time-intensive validation of the data. Pay-walled repositories such as
the Google Maps Platform (<https://developers.google.com/maps>) and
Mapbox (<https://www.mapbox.com/>) do provide validated data that are
regularly updated. Additionally, pestHubMap applications outside of the
US will need to use an alternate source. Our provided web scraping
script only works for the yellowpages.com repository, so if another
source is chosen (e.g., <https://www.pagesjaunes.fr/>), a new script
would need to be written by the user.

**Step 3. Clean hub location data**

The third step is to clean all the hub data by validating (checking for
errors) and tidying (putting the data in a consistent, usable format).
If a user chooses to pay for a service that builds and maintains the
database rather than building it themselves, this step is either skipped
or reduced. Web scraping a free repository (Step 2) can provide
incorrect entries due to variance in how the repository search algorithm
treats different terms. It is therefore necessary to go through the
scraped data by hand to validate each location. At minimum, each entry
should be checked for: correct coordinates, correct address, if the
business is still open, if it is in the desired city/county/state, and
if it is the correct hub type. For this validation step, the pestHubMap
cleaning python script takes the CSV of locations from Step 2 and
automatically displays each entry on Google Maps. The validator cross
references the information on Google Maps with the information scraped
from the repository to decide if an entry is correct or to make
appropriate corrections. To keep track of this process, version control
and a strict protocol for validation are recommended, and we provide
such a protocol in the pestHubMap repository. Finally, validated data
tables are tidied and joined with consistent column names so all tables
have the same structure for the database (Step 4). Often, we queried
yellowpages.com for a search term by a smaller geopolitical unit than
the pestHubMap application we produced (i.e., county vs state). Each of
these queries produced an individual table at the smaller units that
needed to be joined. We have included code written in the R statistical
programming language (R Core Team 2021) in the pestHubMap repository
that conducts the data tidying step which also contains checkpoints that
correct any human error from the validation process.

**Step 4. Build SQL database backend**

The fourth step is to put the verified and harmonized individual data
tables for each hub type are into a relational database built using SQL
(Structured Query Language). We used the free interface tool phpMyAdmin
(<https://www.phpmyadmin.net/>) to manage the SQL database, so users do
not need to know SQL for this step. Here the user defines a consistent
data table structure and uploads each hub data CSV file by hand to the
database. This backend database is hosted online, and using relational
database keys, it is queried by the frontend user interface of the
application (Step 5). No separate backend or SQL integration is required
for other spatial data (e.g., geopolitical border shapefiles from Step
1). These files are uploaded and stored within the file directory of the
hosted application and the user interface loads these data directly.

**Step 5. Build user interface frontend**

The fifth step is to build the frontend interactive map (Fig. 2). We
developed JavaScript code that leverages the leaflet map library that
clusters hubs based on zoom level as the user zooms in and out of the
map (Agafonkin 2018). The code, which is provided in the pestHubMap
repository, first deploys a chosen base map. Second, the spatial data
stored within the file directory are layered on the map (e.g., county
borders, establishment risk layers; see Step 1). A dropdown menu is
added to hide and display non-hub spatial data layers. Third, the
locations of the hub properties are mapped based on the data in the SQL
database (step 4). For each hub type there is an individualized icon
marker and a popup with the business name and address from the SQL
database and a link to driving directions. The code produces links to
the location in Google Maps that can be configured using either
coordinates or the address found in the SQL database. These links can be
used to obtain directions to the hub locations for the field surveyors.
Buttons are added below the map to toggle the display of each hub type.

**Step 6. Deploy the application**

To deploy a pestHubMap application, the data and code created in the
preceding steps must be hosted online. First, the application should be
deployed locally on the user’s machine to troubleshoot and correct
issues with the data files, code, or file structure. To do this, all
files must be present on the machine in the same file structure as the
web\_interface folder on our GitHub repository. The database must be
connected to the application by making basic SQL calls to the database
within the sqlaccess.php file. Users should then test the application
locally by opening the index.php file in a web browser via a local
hosting environment,. We used the free software stack Wampserver which
improves local hosting functionality, as it includes interfaces to MySQL
and phpMyAdmin. A web hosting service is needed to publish the
application and make it available to users online. By doing this, users
will not need to have the files on their own machine, which allows you
to securely control the information disseminated to stakeholders.
Folders should be uploaded to the server in the same structure as the
local application package.

**Step 7. Gather feedback from stakeholders**

Once a usable application is produced, the app is refined by regularly
soliciting feedback from stakeholders to incorporate new information and
field insights. Revising the app based on feedback keeps it up to date
and ensures it is accessible to operations coordinators and survey
personnel. Without this collaboration and user accessibility, the
application is not useful for making management decisions in the field
(Gaydos et al. 2021). During meetings with stakeholders, the application
maintainer should demonstrate the app, answer questions, and request
feedback. Steps 1-6 would then be repeated as necessary to satisfy
stakeholder needs.

## Our Applications

Our SLF Dashboard with all applications:
[slf.iecolab.org/](https://iecolab.org/slfDashboard/index.html)

1.  Pennsylvania pestHubMap:
    <https://iecolab.org/lanternfly-webapp/SLF_Points_of_Interest/index.php>
2.  California pestHubMap:
    <https://iecolab.org/slfDashboard/california_risk.html>
3.  Indiana pestHubMap:
    <https://iecolab.org/lanternfly-webapp-indiana/SLF_Points_Indiana/index.php>
4.  Ohio
    pestHubMap:<https://iecolab.org/lanternfly-webapp-cleveland/SLF_Points_Cleveland/index.php>
5.  Chicagoland pestHubMap:
    <https://iecolab.org/lanternfly-webapp-chicago/SLF_points_Chicago/index.php>
6.  Virginia pestHubMap:
    <https://iecolab.org/lanternfly-webapp-va/SLF_Points_VA/index.php>

## Citation

Ramirez, V.A., Zangakis, E. J., Abernethy, R. D., Hanley, M., Stuart, J.
R., Gleditsch, J. M., & Helmus, M.R. pestHubMap: A framework to design
interactive applications to map properties that spread invasive species.
*In prep.*
