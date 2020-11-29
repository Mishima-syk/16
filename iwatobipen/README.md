## RDKit - DB integration


### requirements

- install with conda
  - postgresql
  - rdkit
  - rdkit-postgresql

- install with pip
  - razi

- ChEMBLDB

### Basic usage

- install packages

```
$ conda activate [your env]
$ conda install -c anaconda postgresql==10.5
$ conda install -c rdkit rdkit
$ conda install -c iwatobipen rdkit-postgresql # for rdkit 20200901
$ git clone https://github.com/rvianello/razi.git
$ cd razi
$ pip install -e .
```

- launch postgresql and install chembldb

```
$ postgresql -D [your pgdata dir]
$ psql -d postgres
# create database chembl_27;
# \q

$ wget ftp://ftp.ebi.ac.uk/pub/databases/chembl/ChEMBLdb/latest/chembl_27_postgresql.tar.gz
$ tar -vxf  chembl_27_postgresql.tar.gz
$ cd chembl_27_postgresql/chembl_27/chemb_27_postgresql
$ pg_restore --no-owner -h HOST -p PORT -U USERNAME -d chembl_27 chembl_27_postgresql.dmp

$ psql -d chembl_27;
chembl_27=# create extension if not exists rdkit;
chembl_27=# create schema rdk;
chembl_27=# select * into rdk.mols from (select molregno,mol_from_ctab(molfile::cstring) m  from compound_structures) tmp where m is not null;
chembl_27=# create index molidx on rdk.mols using gist(m);
CREATE INDEX
chembl_27=# alter table rdk.mols add primary key (molregno);
ALTER TABLE
chembl_27=# select molregno,torsionbv_fp(m) as torsionbv,morganbv_fp(m) as mfp2,featmorganbv_fp(m) as ffp2 into rdk.fps from rdk.mols;
chembl_27=# create index fps_ttbv_idx on rdk.fps using gist(torsionbv);
CREATE INDEX
chembl_27=# create index fps_mfp2_idx on rdk.fps using gist(mfp2);
CREATE INDEX
chembl_27=# create index fps_ffp2_idx on rdk.fps using gist(ffp2);
CREATE INDEX
chembl_27=# alter table rdk.fps add primary key (molregno);
ALTER TABLE
```

### Example usege

- please check notebook in the folder

### Useful links

- [ChEMBL](https://www.ebi.ac.uk/chembl/)
- [RDKit postgresql](https://www.rdkit.org/docs/Cartridge.html)
- [pychembldb](https://pypi.org/project/pychembldb/)
- [pychembldb my fork](https://github.com/iwatobipen/pychembldb/blob/raziintegration/pychembldb/__init__.py)
- [razi tutorial](https://github.com/yamasakih/docker-compose-razi)
- [razi](https://github.com/rvianello/razi/blob/master/docs/database_creation_tutorial.rst)
- [about ChEMBL by @fmkz\_\_\_](https://www.slideshare.net/KazufumiOhkawa/chembl)
