## Problems:
1. How many types of tigers can be found in the taxonomy table of the dataset? What is the "ncbi_id" of the Sumatran Tiger? (hint: use the biological name of the tiger)
2. Find all the columns that can be used to connect the tables in the given database.  
3. Which type of rice has the longest DNA sequence? (hint: use the rfamseq and the taxonomy tables)
4. We want to paginate a list of the family names and their longest DNA sequence lengths (in descending order of length) where only families that have DNA sequence lengths greater than 1,000,000 are included. Give a query that will return the 9th page when there are 15 results per page. (hint: we need the family accession ID, family name and the maximum length in the results)

## Solution 1: 
```sql
SELECT COUNT(DISTINCT species) AS tiger_types
FROM taxonomy WHERE species LIKE '%tiger%'
OR tax_string LIKE '%tiger%'
OR tree_display_name LIKE '%tiger%'
OR align_display_name LIKE '%tiger%';
```
 ![image](https://github.com/user-attachments/assets/70dcaf6c-f78f-4aa5-aaee-261f122d9186)

```sql
SELECT ncbi_id FROM taxonomy WHERE
species LIKE '%Panthera tigris sumatrae%';
```

![image](https://github.com/user-attachments/assets/8c5fb8e3-7733-4abe-81a9-909d015994fb)

## Solution 2:
1. **`rfamseq.ncbi_id` ↔ `taxonomy.ncbi_id`** : Links a sequence (`rfamseq`) to its corresponding taxonomic classification in the `taxonomy` table via the NCBI Taxonomy ID.
2. **`family.rfam_acc` ↔ `clan_membership.rfam_acc`** : Connects each RNA family (`family`) to its membership in a clan (`clan_membership`) using the Rfam accession number.
3. **`clan.clan_acc` ↔ `clan_membership.clan_acc`** : Connects each RNA clan (`clan`) to its members in the `clan_membership` table using the clan accession number.
4. **`full_region.rfam_acc` ↔ `family.rfam_acc`** : Links annotated sequence regions (`full_region`) to their respective RNA family definitions in the `family` table.
5. **`full_region.rfamseq_acc` ↔ `rfamseq.rfamseq_acc`** : Links sequence annotations (`full_region`) to specific sequence entries in `rfamseq` via sequence accession number.

## Solution 3:
```sql
SELECT tx.species, rs.length FROM
rfamseq rs JOIN taxonomy tx ON rs.ncbi_id = tx.ncbi_id
WHERE t.tax_string LIKE '%Oryzeae%' ORDER BY rs.length DESC 
LIMIT 1;
```

## Solution 4:
```sql
SELECT f.rfam_acc, f.rfam_id, MAX(rs.length) AS max_length
FROM family f JOIN full_region fr
ON f.rfam_acc = fr.rfam_acc JOIN rfamseq rs
ON fr.rfamseq_acc = rs.rfamseq_acc
WHERE rs.length > 1000000 GROUP BY f.rfam_acc, f.rfam_id
ORDER BY max_length DESC
LIMIT 15 OFFSET 120;
```
![image](https://github.com/user-attachments/assets/f0e81705-616e-44d4-902f-b7751687175c)

