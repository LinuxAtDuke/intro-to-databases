Introduction to databases
=====================

*Version 1, 2019-09-27*

*https://github.com/LinuxAtDuke/intro-to-databases*

**Instructor**

Andy Ingham (andy.ingham AT duke.edu)

**Table of Contents**

1. [Unit 1: Databases, schema](#unit1)

<a name='unit1'></a>
## Unit 1: Databases, schema

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
	- referential integrity - data types consistent across linking fields (foreign keys)
	- data types (https://dev.mysql.com/doc/refman/5.7/en/data-types.html) should be as prescriptive and compact as possible
	- index creation should be done where needed, but not elsewhere
	- index creation is always faster BEFORE data is loaded into the table
	- verify that data is "reasonably" normalized (e.g., data generally de-duplicated)

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