Introduction to databases
=====================

*Version 3, 2020-08-18*

*https://github.com/LinuxAtDuke/intro-to-databases*

**Instructor**

Andy Ingham (andy.ingham AT duke.edu)

**Table of Contents**

1. [Unit 1: Definitions, uses, and kinds](#unit1)
2. [Unit 2: Schema, data dictionary](#unit2)
3. [Unit 3: Architectures](#unit3)
4. [Unit 4: Data integrity, security, confidentiality](#unit4)
5. [Unit 5: Primer for the Intro to MySQL class (or for practical playing)](#unit5)

<a name='unit1'></a>
## Unit 1: Definitions, uses, and kinds

  * What are data?
	- https://en.wikipedia.org/wiki/Data
	- from least to most abstract:  data > information > knowledge > wisdom
	- "Research data is defined as the recorded factual material commonly accepted in the scientific community as necessary to validate research findings..." (OMB Circular 110 ; https://www.whitehouse.gov/sites/whitehouse.gov/files/omb/circulars/A110/2cfr215-0.pdf)

  * What is a "database"? What is a DBMS (database management system)?
  	- https://en.wikipedia.org/wiki/Database
  	
  * Databases are primarily needed to structure and organize data so that:
  	- associations can be revealed _(e.g., X is related to Y via share attribute Z)_,
  	- patterns can be exposed _(e.g., X is more prevalent than Y by magnitude Z)_,
  	- questions can be answered _(e.g., X is the type of Y that exhibits Z)_,
  	- a repository of information can be stored _(by retaining associated data elements)_

  * Why aren't databases spreadsheets?  Because, _GENERALLY SPEAKING_, databases ...
	- are more powerful (can organize data in more complex ways) and, therefore, can provide better searching/discovery options
	- provide better normalization (if designed well) and, therefore, are more performant as size increases ("scalable")
	- provide more complex integrity checks (rules and constraints about data elements and associations)
	- are more flexible (in how they can be configured)
	- provide more granular options for things like access control and, therefore, better suited to shared usage by disparate groups
	- provide more and better options for external integrations
	- are more focused on searching/storing than tabulating/displaying
	
  * Kinds of databases ... there are quite a few (https://en.wikipedia.org/wiki/Database#Models ), but relational is probably still king 
  	- Relational
  	(https://medium.com/@pocztarski/what-if-i-told-you-there-are-no-tables-in-relational-databases-13d31a2f9677 )
	  * PROS:  highly normalizable (avoids data duplication); stable; mature; consistency after each transaction
	  * CONS:  rigid
	
	- Object / document / NoSQL
	  * PROS:  faster for "big" or distributed data; flexible
	  * CONS:  less standardization for how to interact with data; requires high developer discipline; reasonable use cases are limited
		
	- GraphDBs
	  * PROS:  powerful for specialized use cases (highly interconnected data)	
	  * CONS:  highly complex for both users and developers

	- Key-value stores:
	  * PROS:  simple
	  * CONS:  simple

<a name='unit2'></a>
## Unit 2: Schema, data dictionary

  * Schema development is best done via an ER diagram and/or a whiteboard - consider these:
	- what are the entities? _(the "things" or "concepts" that form the basis of our data)_
	- what relationships do they have with one another?
	- what are the important attributes of the entities?
	- what are the data types and metadata _(is NULL allowed? are there default values?)_ for those attributes?
	- what will determine uniqueness in each table? _(will the primary key be simple or compound?)_
	- what queries are users likely to run? _(this will inform index creation)_
	- what indexes are needed? _(to supplement the primary key)_
	
  * Some (albeit simple and somewhat silly) examples:
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
	  * stored procedures, triggers, views
	  * advanced data types + searching (e.g., XPath)
      * "Hot" backups, point-in-time recovery
  	- security
  	  * how secure are the defaults? (accounts, passwords, settings, allowed connections)
  	  * how straightforward is (or how comfortable are you with) the DB administration?
  	  * are patches regularly released?  _(remembering that updates are only useful if ACTUALLY APPLIED in a timely fashion)_
  	- usability / reporting
  	  * CLI or GUI
  	  * documentation / community-support
  	- integration
  	  * base OS
  	  * middleware / ODBC (Open Database Connectivity)
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
  	- Important for some obvious reasons:
  	  * scientific validity
  	  * potential basis for future work or discoveries
  	  * provide confidence in information
  * (information) security:	CIA (Confidentiality, Integrity, Availability)
  	- Important for some obvious reasons:
  	  * protect intellectual property
  	  * maintain confidentiality, privacy
  	  * provide on-going access for authorized people to allow work to continue
  	  
<a name='unit5'></a>
## Unit 5: Primer for the Intro to MySQL class (or for practical playing)
#### AKA:  Creating a personal Linux VM (Lab 0 from the Intro to MySQL class)

1. Using a web browser, go to *https://vcm.duke.edu/*
2. Login using your Duke NetId.
3. Select "Reserve a VM" (near the middle of the page)
4. On the next screen, select "Lamp Stack" in the dropdown list (under Linux AppStacks) and then "Reserve"
5. After agreeing to the Terms of Use, the VCM web page will display the name of your VM along with available usernames. __You must first connect to the University VPN (if not "on campus"), THEN initiate an ssh session as the Admin User (vcm) -- do this via the "Terminal" app on your Mac or via "PuTTY" (available at https://www.chiark.greenend.org.uk/~sgtatham/putty/ ) on your Windows machine.__
		  
*Example (after establishing a University VPN session, if off campus):* `ssh vcm@vcm-1473.vm.duke.edu` [Answering "yes" to "Are you sure you want to continue connecting (yes/no)?" and then entering the password behind "View Password" when prompted]

  * A brief tangent to discuss architecture

	https://github.com/LinuxAtDuke/Intro-to-MySQL/blob/master/client-server-architecture.pdf

