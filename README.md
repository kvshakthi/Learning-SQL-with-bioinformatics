# Learning-SQL-with-bioinformatics
  Hear is the small effort of getting to know mysql and trying to implement with bioinformatics.  

## 1.CREATE database
  CREATE bioinformatics_db

USE bioinformatics_db

## 2. creating a table

 ## CREATE TABLE genes 
    CREATE TABLE genes
    -- gene info
    (gene_id INT AUTO_INCREMENT PRIMARY KEY,
    gene_name VARCHAR(50) NOT NULL,
    species VARCHAR(50),
    sequence TEXT,
    chromosome INT,
    start_position INT,
    end_position INT);

## CREATE TABLE proteins
    CREATE TABLE proteins
    -- protein info
    (protein_id INT AUTO_INCREMENT PRIMARY KEY,
    protein_name VARCHAR(50),
    gene_id INT,
    sequence TEXT,
    length INT,
    FOREIGN KEY (gene_id) REFERENCES genes(gene_id));

# 3. data manipulation
## -- insert gene data
    INSERT INTO genes (gene_name, species, sequence,
    chromosome, start_position, end_position)
    VALUES ('BRCA1', 'Homo sapiens', 
    'ATGAC...', 17, 43124098, 43125482);
## insert protein data
    INSERT INTO proteins (protein_name, gene_id, sequence, length)
    VALUES ('BRCA1 Protein', 1, 'MKTFF...', 1863);
    SELECT * from genes;

## 4.filtering and querying 
## -- query genes on chromosome 17
    SELECT gene_name, start_position, end_position
    FROM genes
    WHERE chromosome = 17;
 
    SELECT protein_name, length
    FROM proteins
    WHERE length > 1000;
 
## -- joining genes and protein to get full details 
    SELECT g.gene_name, g.species, p.protein_name, p.length
    FROM genes g 
    JOIN proteins p ON g.gene_id=p.gene_id

## -- find genes in spqcific range
    SELECT gene_name, chromosome, start_position, end_position
    FROM genes
    WHERE start_position BETWEEN 43000000 AND 43200000;

## 5. advanced querying
## -- search for genes with partial names
    SELECT *
    FROM genes
    WHERE gene_name LIKE 'BRCa%'; -- purpusefully i have given small case in 
    -- BRCA to check does it work or not

## -- count the number of genes per species
    SELECT species, COUNT(*) AS gene_count
    FROM genes
    GROUP BY species;

## -- find genes without associated proteins
    SELECT g.gene_name
    FROM genes g
    LEFT JOIN proteins p ON g.gene_id= p.gene_id
    WHERE p.protein_id IS NULL;


## 6. constraints
## -- add a unique constraint to avoid duplicate gene names
     ALTER TABLE genes
    ADD CONSTRAINT unique_gene_name UNIQUE (gene_name);

## 7. backup and restore
## -- export the bioinformatics database
    mysqldump -u username -p bioinformatics_db 
    > bioinformatics_backup.sql

## -- import the database
    mysql -u username -p bioinformatics_db 
    < bioinformatics_backup.sql

## 8. eg of stored procedure
## -- get gene details by chromosome
    DELIMITER //
    CREATE PROCEDURE GetGenesByChromosome(IN chromosome_number INT)
    BEGIN
    SELECT gene_name, start_position, end_position
    FROM genes
    WHERE chromosome = chromosome_number;
    END //
    DELIMITER ;
    
## -- call the stored procedure
     CALL GetGenesByChromosome(17);


## 9. performace optimization 
## -- create an index on chromosome for faster queries
    CREATE INDEX idx_chromosome ON genes (chromosome);

## explain a query 
    EXPLAIN SELECT * FROM genes WHERE chromosome = 17;



