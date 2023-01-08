# Cobol programming with VSCode

Coursera: 

https://www.coursera.org/learn/cobol-programming-vscode/course-inbox

All code in this repo is using **enterprise cobol**.


---

# column dependent

- 72 chars a line; 5 key columns
- vertical lines in IDE: margins

| line | 1-6  | 7    | 8-11 | 12-72 | 73-80 |
| ---- | ---- | ---- | ---- | ----  | ---   |
| description | sequence number area | indicator area | A area | B area  | indentification area|

## sequence number area

blank or used to provide context to statements

## indicator area

multi-purpose areas

- comment : asterisk ```*```
- continue a previous line : dash ```-```  or hyphen ```-```
- dubugging line : d
- source code listing formatting: slash ```/```
...

## A area

- divisions
- secions
- paragraphs
- level indicatiors
- other elements that five cobol program their structure

## B area

actual statements sentences clauses ...

## identification area

programmer can use for any purpose, often just left blank

---

# reserved words

https://www.ibm.com/docs/zh-tw/iis/9.1?topic=words-cobol-reserved

---

# Cobol divisions

cobol structure : hierachy

division -> sections -> paragraphs -> sentences -> statements 

## divisions

### identfication division (put name of the program)

### environment division

1. set type of computer environment
2. set the mapping between the files in program and files in actual datasets

### data divisions 
sets up all the data within your program 
eg. files, data from other programs and type of storage

### procedure division
- where all sections and pragraphs go
- where you might perform math or interact with a user

## sections

Different kinds of sections can have user defined names or pro-defined names.
It can be used to give program structure or to make certain info where it needs to be.

## paragraphs

- user defined
- generally represents a specific action made up of smaller steps
- if sth gets called several times, put it in the paragraph
- you can call the paragraph by its name elsewhere in the program

## sentences

- can contain 1 or more statements
- put statements together in a logical container.

eg. 

```ADD 2 TO TOTAL .```

**period** - implicit scope terminator, finish all statements
Exiplicit scope terminator - eg. (start with IF) END-IF

## statements

single directive

usu start with reserved words

eg. add divide move compute

---

# cobol variables

## variable name (data name)

- 30 chars or less
- made up of only letters, digits, and/or hashes
- not a reserved word

e.g inventory1234, ~~top5%~~(cannot have percent sign)

## variable type & num of letters/digits

- numeric  
``` cobol variables
9
```
single numeric value where length is one

```cobol variables
PIC 9(4)
```
variable named year, contain numeric value of 4 digits

- alphabetic 

``` cobol variables
PIC A
```

single alphabetic character ,no length specified so it is 1

- alphanumeric

``` cobol variables
PIC X(6)
```
eight alphanumeric characterics


**maximum length of a numeric picture clause is 18; while an alphabetic pic clause is 255**

## decimal position `V`

``` cobol variables
PIC 9(4)v99
```
can be used to represent 1234.56

``` cobol variables
Pic $9,999V99
```
can be used to represent $1,234.56 (the comma will carry through)

## literals
- contain data that will not chage during the course of the program

- often used to store sth that will be used several times in a program.

e.g name of the company

### figurative literals

zero / zeros
space / spaces
low-value
high-value
null / nulls

---

# Data division

Data division sets up all the data used in the program.

Data relation : add structure to data division.

![[Pasted image 20230106105913.png]]
Sample code for data declaration.

## Level indicator and descriptive entry

In `01 ACCT-FIELDS`, `01` is the level indicator, meaning it is the highest. Data definations is below at `05`.

The file `ACCT-REC` has its own set of  variables with their own level indicators down here.
Below it, it spells out exactly what those account fields are.

## `Move`: moves data from one place into a variable

## `compute`: used to do math with data

![[Pasted image 20230106110733.png]]

`77 WHO` 77 level variables


> When you are running COBOL on a mainframe, you run it by submitting the *JCL*.

--- 

# File handling in COBOL

- use `select` and `assign` clause in environment division
- `FD` statements in the data division
- `open` , `close` , `read into` and `write from` statements in procedure divisions

In environment and data division: describe inputs and outputs
In procedure division: uses the described inputs and outputs to do calculations

```COBOL
*--------------------
 ENVIRONMENT DIVISION.
*--------------------
 INPUT-OUTPUT SECTION.
 FILE-CONTROL.
     SELECT PRINT-LINE ASSIGN TO PRTLINE.
     SELECT ACCT-REC   ASSIGN TO ACCTREC.
```

Above is the **file control paragraph** in the input output section of the environment division.

In the code, we are associating each COBOL internal filename with an external dataset name.
`PRINT-LINE` : internal file name;
`PRTLINE` : external dataset or file

```COBOL
*--------------------
 DATA DIVISION.
*--------------------
 FILE DIVISION.
 FD  PRINT-LINE RECORDING MODE F.
 01  PRINT-REC.
     05  ACCT-NO-O       PIC X(8).
     05  ACCT-LIMIT-O    PIC $$,$$$,$$9.99.
     05  ACCT-BALANCE-O  PIC $$,$$$,$$9.99.
     05  LAST-NAME-O     PIC X(20).
     05  FIRST-NAME-O    PIC X(15).
     05  COMMENTS-O      PIC X(50).
*
 FD  ACCT-REC RECORDING MODE F.
 01  ACCT-FIELDS.
	 05 ACCT-NO       PIC X(8).
	 05 ACCT-LIMIT    PIC S9(7)V99 COMP-3.
	 05 ACCT-BALANCE  PIC S9(7)V99 COMP-3.
	 05  LAST-NAME    PIC X(20).
     05  FIRST-NAME   PIC X(15).
     05  STREET-ADDR  PIC X(25).
	 05  CITY-COUNTY  PIC X(20).
     05  USA-STATE    PIC X(7).
     05  COMMENTS     PIC X(50).
     
```

In data divisions, we define level numbers, variable names, data types and lengths.

`FD` is a reserved word that is used to gibe the COBOL complied more information about those internal filenames.

`PRINT-LINE`, which is linked with the dataset file `PRTLINE` has these fields, types, and lengths.

COBOL running on IBM z mainframe has access to both traditional z/OS datasets and Unix files.

> For now, the data handling is focused on z/OS sequential data storage method.

A **dataset in z/OS** is made up of records. 

Each **record** is like a line within a dataset, with the defined length. 

A line is combined of different **fields**.

## blocks

![[Pasted image 20230106195657.png]]

a block of records loaded into a buffer,  where the overall record length is the size of each field combined

overall block size can be set in the buffer with the block contains clause.

We've said select ACCOUNT-REC with a dash in it, assign to ACCTREC. If we're running this in z/OS using JCL, we need to have a JCL DD statement to link ACCOUNT-REC to that actual dataset. The JCL required for the COBOL compiler to make the connection back to the actual dataset looks something like this, `//ACTREC DD DSN=MY.DATA,DISP=SHR.` It is a data declaration(`DD`) statement.

It says that for `ACCOUNT-REC`, look here at `MY.DATA`, the `DISP=SHR` just means that it expects that the dataset exist before we try to use it, and that other people can use it at the same time.

>More info on JCL:[https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.zconcepts/zconc_whatisjcl.htm](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.zconcepts/zconc_whatisjcl.htm)

---

# Procedure division

Prodecure division: where open files happen in open file section.


```COBOL
*--------------------
 PROCEDURE DIVISION.
*--------------------
 OPEN-FILES.
     OPEN INPUT   ACCT-REC.
     OPEN OUTPUT  PRINT-LINE.
*
 CLOSE-STOP.
     CLOSE ACCT-REC.
     CLOSE PRINT-LINE.
     STOP RUN.
```

- account-rec open for input and print-line open for output
-  close the file when we are done.

closing file can be done during program completion or if the program is big, we can use `CLOSE-STOP`.


```COBOL
 READ-NEXT-RECORD.
     PERFORM READ-RECORD
      PERFORM UNTIL LASTREC = 'Y'
      PERFORM WRITE-RECORD
      PERFORM READ-RECORD
      END-PERFORM
     .
*
 CLOSE-STOP.
     CLOSE ACCT-REC.
     CLOSE PRINT-LINE.
     STOP RUN.
*
 READ-RECORD.
     READ ACCT-REC
     AT END MOVE 'Y' TO LASTREC
     END-READ.
*
 WRITE-RECORD.
     MOVE ACCT-NO TO ACCT-NO-O.
     MOVE ACCT-LIMIT TO ACCT-LIMIT-O.
     MOVE ACCT-BALANCE TO ACCT-BALANCE-O.
     MOVE LAST-NAME TO LAST-NAME-O.
     MOVE FIRST-NAME TO FIRST-NAME-O.
     MOVE COMMENTS TO COMMENTS-O.
     WRITE PRINT-REC.
     
```

`for` or `do` in other languages, cobol uses `PERFORM`

In the `READ-NEXT-RECORD` paragraph, the first thing is to do `READ-RECORD`.  There's no conditions, as it it to be read at least once.

The `READ-RECORD` is defined down below at third paragraph. It is going to read the next record from the file, and if we're at the end put a `Y`, presumably for yes into `LAST-REC`. Then when we're done reading, we have this `END-READ`. Even though that terminates logically and explicitly with the end-read, it also terminates implicitly with that period`.`.

Now going back up into the `READ-NEXT-RECORD`, we're indenting and our first line here is a  `PERFORM UNTIL`.