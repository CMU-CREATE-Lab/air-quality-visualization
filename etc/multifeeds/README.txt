I wanted ACHD multifeeds for wind direction, wind speed, PM 2.5, and PM 10.  To do so, I first had to figure out which
ACHD feeds had the channels I wanted.  ESDR doesn't yet have a way to ask for feeds which contain specific channels, so
I had to resort to querying the DB directly.  Once I had enumerated all the possible channels (not shown here, see the
achd.html file), I did these SQL queries to determine the feed IDs:

   mysql> select id, name from Feeds where productId=1 and channelBounds like '%"SONICWD_DEG%';
   +----+-----------------------------+
   | id | name                        |
   +----+-----------------------------+
   |  1 | Avalon ACHD                 |
   |  3 | North Braddock ACHD         |
   | 26 | Lawrenceville ACHD          |
   | 28 | Liberty ACHD                |
   | 32 | South Fayette ACHD          |
   | 35 | Parkway East Near Road ACHD |
   | 43 | Parkway East Near Road ACHD |
   +----+-----------------------------+
   7 rows in set (0.00 sec)

   mysql> select id, name from Feeds where productId=1 and channelBounds like '%"SONICWS_MPH%';
   +----+-----------------------------+
   | id | name                        |
   +----+-----------------------------+
   |  1 | Avalon ACHD                 |
   |  3 | North Braddock ACHD         |
   | 26 | Lawrenceville ACHD          |
   | 28 | Liberty ACHD                |
   | 32 | South Fayette ACHD          |
   | 35 | Parkway East Near Road ACHD |
   | 43 | Parkway East Near Road ACHD |
   +----+-----------------------------+
   7 rows in set (0.00 sec)

   mysql> select id, name from Feeds where productId=1 and channelBounds like '%"RWS_MPH%';
   +----+--------------------+
   | id | name               |
   +----+--------------------+
   | 26 | Lawrenceville ACHD |
   +----+--------------------+
   1 row in set (0.00 sec)

   mysql> select id, name from Feeds where productId=1 and channelBounds like '%"RWD_DEG%';
   +----+--------------------+
   | id | name               |
   +----+--------------------+
   | 26 | Lawrenceville ACHD |
   +----+--------------------+
   1 row in set (0.00 sec)

   mysql> select id, name from Feeds where productId=1 and (channelBounds like '%"PM10_UG_M3"%' or channelBounds like '%"PM10B_UG_M3"%');
   +----+----------------------------+
   | id | name                       |
   +----+----------------------------+
   |  3 | North Braddock ACHD        |
   | 23 | Flag Plaza ACHD            |
   | 24 | Glassport High Street ACHD |
   | 27 | Lawrenceville 2 ACHD       |
   | 29 | Liberty 2 ACHD             |
   | 30 | Lincoln ACHD               |
   | 31 | Monroeville ACHD           |
   +----+----------------------------+
   7 rows in set (0.00 sec)

   mysql> select id, name from Feeds where productId=1 and (channelBounds like '%"PM25B_UG_M3"%' or channelBounds like '%"PM25_2__UG_M3"%' or channelBounds like '%"PM25_UG_M3"%');
   +----+----------------------+
   | id | name                 |
   +----+----------------------+
   |  1 | Avalon ACHD          |
   | 26 | Lawrenceville ACHD   |
   | 27 | Lawrenceville 2 ACHD |
   | 29 | Liberty 2 ACHD       |
   | 30 | Lincoln ACHD         |
   +----+----------------------+
   5 rows in set (0.00 sec)


I then crafted the multifeed JSON (in this directory) as appropriate, and created the multifeeds in ESDR using these
curl commands:

   curl -X POST -H "Content-Type:application/json" -H "Authorization: Bearer OAUTH_ACCESS_TOKEN_HERE" https://esdr.cmucreatelab.org/api/v1/multifeeds -d @achd_wind_direction.json;
   curl -X POST -H "Content-Type:application/json" -H "Authorization: Bearer OAUTH_ACCESS_TOKEN_HERE" https://esdr.cmucreatelab.org/api/v1/multifeeds -d @achd_wind_speed.json;
   curl -X POST -H "Content-Type:application/json" -H "Authorization: Bearer OAUTH_ACCESS_TOKEN_HERE" https://esdr.cmucreatelab.org/api/v1/multifeeds -d @achd_wind_speed_and_direction.json;
   curl -X POST -H "Content-Type:application/json" -H "Authorization: Bearer OAUTH_ACCESS_TOKEN_HERE" https://esdr.cmucreatelab.org/api/v1/multifeeds -d @achd_pm_25.json;
   curl -X POST -H "Content-Type:application/json" -H "Authorization: Bearer OAUTH_ACCESS_TOKEN_HERE" https://esdr.cmucreatelab.org/api/v1/multifeeds -d @achd_pm_10.json;

