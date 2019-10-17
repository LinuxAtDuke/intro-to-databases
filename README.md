Introduction to databases
=====================

*Version 1, 2019-10-22*

*https://github.com/LinuxAtDuke/intro-to-databases*

**Instructor**

Andy Ingham (andy.ingham AT duke.edu)

**Table of Contents**

1. [Unit 1: Definitions, uses, and kinds](#unit1)
2. [Unit 2: Schema, data dictionary](#unit2)
3. [Unit 3: Architectures](#unit3)
4. [Unit 4: Data integrity, security, confidentiality](#unit4)
5. [Unit 5: Advanced topics (time permitting)](#unit5)

<a name='unit1'></a>
## Unit 1: Definitions, uses, and kinds

  * What are data?
	- https://en.wikipedia.org/wiki/Data

  * What is a "database"?
  	- https://en.wikipedia.org/wiki/Database
  	
  * Databases are primarily needed to structure and organize data so that:
  	- associations can be revealed,
  	- patterns can be exposed,
  	- questions can be answered,
  	- a repository of information can be stored
  	
  * Kinds of databases (https://en.wikipedia.org/wiki/Database#Classification )
  	- Relational
  	(https://medium.com/@pocztarski/what-if-i-told-you-there-are-no-tables-in-relational-databases-13d31a2f9677 )
	  * PROS:  highly normalizable (avoids data duplication); stable; mature; consistency after each transaction
	  * CONS:  rigid
	
	- Object / document / NoSQL
	  * PROS:  faster for large or distributed data; flexible
	  * CONS:  less standardization for how to interact with data; requires high developer discipline; reasonable use cases are limited
		
	- GraphDBs
	  * PROS:  powerful for specialized use cases (highly interconnected data)	
	  * CONS:  highly complex for both users and developers

	- Key-value stores:
	  * PROS:  simple
	  * CONS:  simple 

  * Why databases aren't spreadsheets.  GENERALLY, databases:
	- are more powerful (can organize data in more complex ways)
		* and, therefore, can provide FAR better searching/discovery options
	- provide better normalization (if designed well)
		* and, therefore, are more performant as size increases
	- provide more complex integrity checks (rules and constraints)
	- are more flexible (in how they can be configured)
	- provide more granular options for things like access control
	- provide more and better options for external integrations
	- are more focused on storing than tabulating/displaying

<a name='unit2'></a>
## Unit 2: Schema, data dictionary

  * Schema development is best done via an ER diagram and/or a whiteboard - consider these:
	- what are the entities?
	- what relationships do they have with one another?
	- what are the important attributes of the data elements?
	- what are the data types and metadata (is NULL allowed? defaults?) for the attributes
	- what will determine uniqueness in each table? (simple or compound primary keys?)
	- what queries are users going to run? (which will inform index creation)
	- what indexes are needed (beyond those for the primary keys)?
	
  * Some examples:
	- https://www.edrawsoft.com/templates/pdf/pet-store-er-diagram.pdf
		- https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/pet-store-example-schemaOwner.pdf
		- https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/pet-store-example-schemaPet.pdf
		- https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/pet-store-example-schemaPetClinic.pdf
		- https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/pet-store-example-schemaPetStore.pdf
		- https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/pet-store-example-schemaTreatments.pdf
	- https://www.safaribooksonline.com/library/view/learning-mysql/0596008643/ch04s04.html

  * A tutorial to help with schema development:
  	- http://www.anchor.com.au/hosting/support/CreatingAQuickMySQLRelationalDatabase

  * Fine-tuning of schema...
	- referential integrity - data types consistent across linking fields (foreign keys) (https://en.wikipedia.org/wiki/Data_integrity )
	- data types (https://dev.mysql.com/doc/refman/5.7/en/data-types.html) should be as prescriptive and compact as possible
	- index creation should be done where needed, but not elsewhere
	- index creation is always faster BEFORE data are loaded into the table
	- verify that data are "reasonably" normalized (e.g., data generally de-duplicated)

  * Some examples
  
	_mysql>>_ describe LCL_genotypes;

	| Field    | Type         | Null | Key | Default | Extra |
	|:---------|:-------------|:-----|:----|:--------|:------|
	| IID      | varchar(16)  | NO   | PRI | NULL    |       |
	| SNPpos   | varchar(512) | NO   | PRI | NULL    |       |
	| rsID     | varchar(256) | NO   | MUL | NULL    |       |
	| genotype | varchar(512) | NO   |     | NULL    |       |

	_mysql>>_ describe phenotypes;

	| Field             | Type           | Null | Key | Default | Extra |
	|:------------------|:---------------|:-----|:----|:--------|:------|
	| LCL\_ID            | varchar(16)    | NO   | PRI | NULL    |       |
	| phenotype         | varchar(128)   | NO   | PRI | NULL    |       |
	| phenotypic\_value1 | decimal(20,10) | YES  |     | NULL    |       |
	| phenotypic\_value2 | decimal(20,10) | YES  |     | NULL    |       |
	| phenotypic\_value3 | decimal(20,10) | YES  |     | NULL    |       |
	| phenotypic_mean   | decimal(20,10) | YES  |     | NULL    |       |

	_mysql>>_ describe snp;

	| Field              | Type                | Null | Key | Default | Extra |
	|:-------------------|:--------------------|:-----|:----|:--------|:------|
	| rsID               | varchar(256)        | NO   | PRI | NULL    |       |
	| Chromosome         | tinyint(3) unsigned | NO   |     | NULL    |       |
	| Position           | int(10) unsigned    | NO   |     | NULL    |       |
	| Allele1            | varchar(128)        | NO   |     | NULL    |       |
	| Allele2            | varchar(128)        | NO   |     | NULL    |       |
	| DistanceToNearGene | varchar(32)         | NO   |     | NULL    |       |
	| Gene               | varchar(32)         | NO   |     | NULL    |       |
	| SNPtype            | varchar(64)         | NO   |     | NULL    |       |



<a name='unit3'></a>
## Unit 3: Architectures

  * suitability (or, So which database to choose?  (SQLite vs Postgres vs MySQL vs ORACLE vs ...))
  	- functionality
  	  * store and retrieve; CRUD (Create, Read, Update, Delete)
  	  * partitioning, replication, MVCC (Multi-Version Concurrency Control)
	  * advanced data types + searching (e.g., XPath)
	  * stored procedures, triggers, views
      * "Hot" backups, point-in-time recovery
  	- security
  	  * secure defaults (accounts, passwords, settings, allowed connections)
  	  * straightforward administration
  	  * regularly patched
  	- usability / reporting
  	  * CLI or GUI
  	  * documentation / community-support
  	- integration
  	  * base OS
  	  * middleware / ODBC
  	  * reporting tools
  	- cost
  	  * up-front (hardware, licensing)
  	  * pay-as-you-go (cloud options)
  	- support / hosting / future development (bug fixes+enhancements+security)
  	- scalability (DBMS as well as underlying environments)
  	  * storage needs
  	  * transaction rates


<a name='unit4'></a>
## Unit 4: Data integrity, security, confidentiality

  * integrity:  ACID (Atomicity, Consistency, Isolation, and Durability) (https://en.wikipedia.org/wiki/ACID)
  * (information) security:	CIA (Confidentiality, Integrity, Availability)
  
<a name='unit5'></a>
## Unit 5: Advanced topics