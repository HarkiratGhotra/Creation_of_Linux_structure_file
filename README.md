SHEL SCRIPTINGL
=========

Licensing Information: READ LICENSE  
---
Project Source can be download from https://github.com/HarkiratGhotra/Creation_of_Linux_structure_file
---

This project is related to create 10,000 files with 2000 rows each with 50 columns and then moving those files on Hadoop

* Here I have created input file with numbers and then convert those numbers in rows and columns order, then I converted those files into CSV  

**Steps to create files with multiple records in structured way**

* First create a "filename.sh" in your directory
'''
vi filename.sh
'''

* For creating n number of files with m numbers of records in output file in the form of rows and column 
'''
#for creating 'n' number of files, here n = 10,000
for ((i=1;i<=10000;i++))
        do
#for creating m number of records, here m = 100,000
        for((j=1;j<=100000;j++))
                do
#inserting each record into file
                echo $j >>create_input_file$i;
                done;
#'awk' separates the column based on row ('nr'), here nr = 2000, which will create another column after inserting '2000' row in one column
        awk -v nr=2000 '{a[NR]=$0} END{for (i=1;i<=nr;i++) {for (j=i;j<=NR;j+=nr  )printf "%d  ",a[j]; print" "}}' create_input_file$i >output_file$i
        done;
'''

* Delete the input file, which I have created, as I need only output file, so in order to remove unused file, removing create_input_file
'''
#deleting input file, as we only require output file
for delete_input_file in create_input_file*
        do
        rm $delete_input_file;
        done;
'''

* Converting the outfile into CSV 
'''
#converting the output into CSV
for csv in output_file*
        do
        sed -i -e 's/  /,/g' $csv;
        done;

#removing the last comma from each row
for last_comma in output_file*
        do
        sed -i -e 's/,[[:blank:]]*$//g' $last_comma;
        done;
'''

* compressing the output file as we need to combine all the output file and send it to Hadoop
'''
#compressing the output file
tar -zcvf output.archive.tar.gz output*
'''

* Creating a directory in Hadoop and copyFromLocal on Hadoop
'''
#crating the directory in hadoop "assignment_linux"
hadoop fs -mkdir /user/cloudera/assignment_linux

#cp the "output.archive.tar.gz" into hadoop
hadoop fs -put output.archive.tar.gz /user/cloudera/assignment_linux
'''
