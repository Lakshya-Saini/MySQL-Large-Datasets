## MySQL-Large-Datasets

Provided some large IMDB test datasets for MySQL for practice purpose.

### Prerequisites

1. MySQL setup on local
2. Disk space available (~10GB)
3. Files are on your local machine, e.g. C:/Users/Lakshya/Downloads/

### How to setup 

#### 1. Download Datasets and Extract Files:
https://datasets.imdbws.com/

#### 2. Login to Mysql CLI:

```
mysql --local-infile=1 -u root -p
```

#### 3. Enable Local Infile (if disabled)

- Check in MySQL:
```
SHOW VARIABLES LIKE 'local_infile';
```

- If OFF, enable it by running:
```
SET GLOBAL local_infile = 1;
```

#### 4. Create new DB:

```
CREATE DATABASE test_large_dataset;
USE test_large_dataset;
```

#### 5. Create MySQL Schemas:

```
CREATE TABLE title_akas (
    titleId VARCHAR(20),
    ordering INT,
    title TEXT,
    region VARCHAR(10),
    language VARCHAR(20),
    types TEXT,              -- Array, stored as comma-separated values
    attributes TEXT,         -- Array, stored as comma-separated values
    isOriginalTitle BOOLEAN
);

CREATE TABLE title_basics (
    tconst VARCHAR(20),
    titleType VARCHAR(50),
    primaryTitle TEXT,
    originalTitle TEXT,
    isAdult BOOLEAN,
    startYear YEAR,
    endYear YEAR,
    runtimeMinutes INT,
    genres TEXT              -- Array, stored as comma-separated values
);

CREATE TABLE title_crew (
    tconst VARCHAR(20),
    directors TEXT,          -- Array of nconsts, stored as comma-separated values
    writers TEXT             -- Array of nconsts, stored as comma-separated values
);

CREATE TABLE title_episode (
    tconst VARCHAR(20),
    parentTconst VARCHAR(20),
    seasonNumber INT,
    episodeNumber INT
);

CREATE TABLE title_principals (
    tconst VARCHAR(20),
    ordering INT,
    nconst VARCHAR(20),
    category VARCHAR(100),
    job TEXT,
    characters TEXT          -- JSON string or comma-separated
);

CREATE TABLE title_ratings (
    tconst VARCHAR(20),
    averageRating FLOAT,
    numVotes INT
);

CREATE TABLE name_basics (
    nconst VARCHAR(20),
    primaryName VARCHAR(255),
    birthYear YEAR,
    deathYear YEAR,
    primaryProfession TEXT,     -- Array, stored as comma-separated values
    knownForTitles TEXT         -- Array, stored as comma-separated tconsts
);
```

#### 6. Import .tsv Files (one by one)

**Note: Wait for data load to be completed after you run below queries. It might take upto 15min to complete (depending on data size).**

- Format:
```
LOAD DATA LOCAL INFILE '{YOUR_FILE_ABSOLUTE_PATH}'
INTO TABLE {TABLE_NAME}
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(titleId, ordering, title, region, language, types, attributes, isOriginalTitle)
SET
    isOriginalTitle = NULLIF(isOriginalTitle, '\N');
```

- Example:
```
LOAD DATA LOCAL INFILE 'C:/Users/Lakshya/Downloads/title.basics.tsv'
INTO TABLE title_basics
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(tconst, titleType, primaryTitle, originalTitle, isAdult, startYear, endYear, runtimeMinutes, genres)
SET
    startYear = NULLIF(startYear, '\N'),
    endYear = NULLIF(endYear, '\N'),
    runtimeMinutes = NULLIF(runtimeMinutes, '\N');
```

**You'll get a message like this, once query is completed**

```
Query OK, 92101010 rows affected (14 min 36.62 sec)
Records: 92101010  Deleted: 0  Skipped: 0  Warnings: 0
```
