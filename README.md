# Sparql FDW for PostgreSQL

sparql_fdw is experimantal code, use it at your own risk. But please consider
to open a bug report on the Github repository, if you stumble upon something,
that doesn't work as expected.



## PostgreSQL support

[![version](https://img.shields.io/badge/PostgreSQL-12-blue.svg)]()
[![version](https://img.shields.io/badge/PostgreSQL-13-blue.svg)]()
[![version](https://img.shields.io/badge/PostgreSQL-14-blue.svg)]()
[![version](https://img.shields.io/badge/PostgreSQL-15-blue.svg)]()
[![version](https://img.shields.io/badge/PostgreSQL-16-blue.svg)]()
[![version](https://img.shields.io/badge/PostgreSQL-17-blue.svg)]()

[![Lang](https://img.shields.io/badge/Language-Python3-green.svg)]()
[![PostgreSQL](https://img.shields.io/badge/License-PostgreSQL-green.svg)]()
[![Extension](https://img.shields.io/badge/Extension-PostgreSQL-green.svg)]()

## Installation

### Installation of multicorn

As the original multicorn sources are somewhat outdated, use multicorn2 instead.
Please follow the installation guide on https://github.com/pgsql-io/multicorn2

### CentOS/RockyLinux install with postgresql installed from PGDG repository

#### Installation of needed packages

```bash
yum install -y epel-release
yum install -y python3-setuptools git python3-pip python3-dateutil
pip3 install --break-system-packages --upgrade pip
pip3 install --break-system-packages SPARQLWrapper python-dateutil
```

# Clone and execute 'python setup.py install' as root

```bash
git clone https://github.com/al1radix/sparql_fdw.git
cd sparql_fdw
python3 setup.py install
```
### Debian/Ubuntu installation

#### Install needed Python packages

```bash
sudo apt install -y python3-setuptools git python3-pip python3-dateutil
sudo pip3 install --break-system-packages --upgrade pip
sudo pip3 install --break-system-packages SPARQLWrapper python-dateutil
pip3 install --break-system-packages python-dateutil


```

## Clone sparql_fdw and instll it with pip3

```bash
git clone https://github.com/al1radix/sparql_fdw.git
cd sparql_fdw
sudo pip3 install --break-system-packages .
```


## Usage
### create multicorn extension
```sql
create extension multicorn;
```

### create server
```sql
CREATE server wikidata foreign data wrapper multicorn
	options ( wrapper 'sparqlfdw.SparqlForeignDataWrapper', endpoint 'https://query.wikidata.org/sparql');

CREATE server dbpedia foreign data wrapper multicorn options ( wrapper 'sparqlfdw.SparqlForeignDataWrapper', endpoint 'http://dbpedia.org/sparql' );
```

### Create foreign tables with sparql query
* matching is done between table's column names and sparql selected variables names

```sql
-- eyes colors from wikidata
CREATE FOREIGN TABLE eyes ( "eyeColorLabel" json, count int, rien text )
server wikidata options ( sparql '
	SELECT ?eyeColorLabel (COUNT(?human) AS ?count)
	WHERE { ?human wdt:P31 wd:Q5.  ?human wdt:P1340 ?eyeColor.
	SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". } }
	GROUP BY ?eyeColorLabel
')
;
```

The actual implementation is simplist and dosn't push down restriction, group by, order by nor limit to sparql
