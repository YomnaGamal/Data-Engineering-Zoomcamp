

 # Homework Answers: Docker and Terraform 

 --- 

 ## Question 1: Understanding docker first run 

 ### 1.1. Run a Python Container 
 Run a Python container in interactive mode with `bash` as the entry point using command: 

 ```bash 
 docker run -it --entrypoint bash python:3.12.8 
 ``` 

 ### 1.2. Check Pip Version 
 Check the version of `pip` by running: 

 ```bash 
 pip --version 
 ``` 

 **Output:** 
 ``` 
 pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12) 
 ``` 
**![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXegmt9UA8SexhzeUcV6FyhRXw9-moXALVA_zPyEO7sPdWTjGsAqZvkZbnEZxKYflNuK1pvUTZ_z6bcJFPUwFFZ7FOtEtBmcXCy0ESmGU9MQ1MEwBD51uZ1JmhDTCxD0Klv6E05neg?key=J3-9cZ5OnmjbsRnNy13MmN8M)**
 --- 

 ## Question 2: Understanding Docker networking and docker-compose 

 ### 2.1. Create given docker-compose.yaml to create a custom container have **pgadmin** and **postgres**: 

 ```docker-compose.yaml 
services:
  db:
    container_name: postgres
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'postgres'
      POSTGRES_DB: 'ny_taxi'
    ports:
      - '5433:5432'
    volumes:
      - vol-pgdata:/var/lib/postgresql/data

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: "pgadmin@pgadmin.com"
      PGADMIN_DEFAULT_PASSWORD: "pgadmin"
    ports:
      - "8080:80"
    volumes:
      - vol-pgadmin_data:/var/lib/pgadmin  

volumes:
  vol-pgdata:
    name: vol-pgdata
  vol-pgadmin_data:
    name: vol-pgadmin_data
 ``` 

 ### 2.2. Run Docker Compose 
 Run: 

 ```bash 
 docker-compose up 
 ``` 

 ### 2.3. Docker networking 
 What is the `hostname` and `port` that **pgadmin** should use to connect to the postgres database?
```Answer 
 db:5432 
 ``` 

 --- 

 ## Question 3: Trip Segmentation Count 

During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, **respectively**, happened:
1. Up to 1 mile
  ```Answer 
 SELECT 
    COUNT(*) AS trips_up_to_1_mile
FROM 
    green_taxi_data
WHERE 
    trip_distance <= 1.0
    AND lpep_pickup_datetime >= '2019-10-01'
    AND lpep_dropoff_datetime < '2019-11-01'; 
 ``` 
2. In between 1 (exclusive) and 3 miles (inclusive),
  ```Answer 
SELECT 
    COUNT(*) AS trips_between_1_and_3_miles
FROM 
    green_taxi_data
WHERE 
    trip_distance > 1.0
    AND trip_distance <= 3.0
    AND lpep_pickup_datetime >= '2019-10-01'
    AND lpep_dropoff_datetime < '2019-11-01';
 ``` 
    
3. In between 3 (exclusive) and 7 miles (inclusive),
  ```Answer 
SELECT 
    COUNT(*) AS trips_between_3_and_7_miles
FROM 
    green_taxi_data
WHERE 
    trip_distance > 3.0
    AND trip_distance <= 7.0
    AND lpep_pickup_datetime >= '2019-10-01'
    AND lpep_dropoff_datetime < '2019-11-01';
 ``` 
4. In between 7 (exclusive) and 10 miles (inclusive),
  ```Answer 
SELECT 
    COUNT(*) AS trips_between_7_and_10_miles
FROM 
    green_taxi_data
WHERE 
    trip_distance > 7.0
    AND trip_distance <= 10.0
    AND lpep_pickup_datetime >= '2019-10-01'
    AND lpep_dropoff_datetime < '2019-11-01';
 ``` 
5. Over 10 miles 
  ```Answer 
 SELECT 
    COUNT(*) AS trips_over_10_miles
FROM 
    green_taxi_data
WHERE 
    trip_distance > 10.0
    AND lpep_pickup_datetime >= '2019-10-01'
    AND lpep_dropoff_datetime < '2019-11-01'; 
 ``` 

Answer:
  ```Answer 
104,802; 198,924; 109,603; 27,678; 35,189
```

 --- 

 ## Question 4: Longest trip for each day

Which was the pick up day with the longest trip distance?
Use the pick up time for your calculations.

Tip: For every day, we only care about one single trip with the longest distance. 
Answer: 
- 2019-10-31
with max_distance 515.89

 ```Answer 
WITH max_trip_per_day AS (
    SELECT
        DATE(lpep_pickup_datetime) AS pickup_date,
        MAX(trip_distance) AS max_distance
    FROM
        green_taxi_data
    GROUP BY
        DATE(lpep_pickup_datetime)
)
SELECT
    pickup_date,
    max_distance
FROM
    max_trip_per_day
ORDER BY
    max_distance DESC
LIMIT 1;
```
 --- 

 ## Question 5: Three biggest pickup zones

Which were the top pickup locations with over 13,000 in
`total_amount` (across all trips) for 2019-10-18?

Consider only `lpep_pickup_datetime` when filtering by date.
 Answer: 
- East Harlem North, East Harlem South, Morningside Heights
 ```Answer 
SELECT
    z."Borough",
    z."Zone",
    SUM(g.total_amount) AS total_amount_sum
FROM
    green_taxi_data g
JOIN
    zones z ON g."PULocationID" = z."LocationID"
WHERE
    DATE(g.lpep_pickup_datetime) = '2019-10-18'
GROUP BY
    z."Borough", z."Zone"
HAVING
    SUM(g.total_amount) > 13000
ORDER BY
    total_amount_sum DESC;
```
 --- 

 ## Question 6:  Largest tip

For the passengers picked up in October 2019 in the zone
named "East Harlem North" which was the drop off zone that had
the largest tip?

Note: it's `tip` , not `trip`

We need the name of the zone, not the ID.
Answer: 
- JFK Airport

 ```Answer  
SELECT 
    z."Zone" AS dropoff_zone,
    MAX(g.tip_amount) AS max_tip
FROM 
    green_taxi_data g
JOIN 
    zones z ON g."DOLocationID" = z."LocationID"
WHERE 
    g."PULocationID" = (
        SELECT "LocationID" 
        FROM zones 
        WHERE "Zone" = 'East Harlem North'
    )
    AND DATE_TRUNC('month', g.lpep_pickup_datetime::timestamp) = '2019-10-01'
GROUP BY 
    z."Zone"
ORDER BY 
    max_tip DESC
LIMIT 1;
```

## Question 7. Terraform Workflow

Which of the following sequences, **respectively**, describes the workflow for: 
1. Downloading the provider plugins and setting up backend,
2. Generating proposed changes and auto-executing the plan
3. Remove all resources managed by terraform`

Answer:
- terraform init, terraform apply -auto-approve, terraform destroy
