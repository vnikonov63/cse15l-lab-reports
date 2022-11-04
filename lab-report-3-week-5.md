# The analysis of the `find` command

In general this command is used to recursivelly traverse through the given path and find all the files in that directory and consecutive subdirectories.

**Basic example**

The following code:

`find technical > technicalFindExample.txt`

`less technicalFindExample.txt`

has the following output:

```
technical
technical/government
technical/government/About_LSC
technical/government/About_LSC/LegalServCorp_v_VelazquezSyllabus.txt
technical/government/About_LSC/Progress_report.txt
technical/government/About_LSC/Strategic_report.txt
technical/government/About_LSC/Comments_on_semiannual.txt
technical/government/About_LSC/Special_report_to_congress.txt
technical/government/About_LSC/CONFIG_STANDARDS.txt
technical/government/About_LSC/commission_report.txt
technical/government/About_LSC/LegalServCorp_v_VelazquezDissent.txt
technical/government/About_LSC/ONTARIO_LEGAL_AID_SERIES.txt
technical/government/About_LSC/LegalServCorp_v_VelazquezOpinion.txt
technical/government/About_LSC/diversity_priorities.txt
technical/government/About_LSC/reporting_system.txt
technical/government/About_LSC/State_Planning_Report.txt
technical/government/About_LSC/Protocol_Regarding_Access.txt
technical/government/About_LSC/ODonnell_et_al_v_LSCdecision.txt
technical/government/About_LSC/conference_highlights.txt
technical/government/About_LSC/State_Planning_Special_Report.txt
technical/government/Env_Prot_Agen
technical/government/Env_Prot_Agen/multi102902.txt
technical/government/Env_Prot_Agen/section-by-section_summary.txt
```
Here we display the directory itself, all subdirectories, and files within the subdirectory. Save it in the temporary file and then display the first page of wthe file using the `less` utility.

For comparing command line argument line behaviourr with the initial result I will also put some statistic about the 'vanilla' behaviour. Lets see how many subdirs and files are in the `./technical` directory with the command `wc technicalFindExample.txt `.

```
    1402    1402   54468 technicalFindExample.txt
```

Answer is: 1402

## Command line argument `-maxdepth`

Number of recursive steps we would do in searching for the files.

```
find technical -maxdepth 1 > technicalFindExample.txt
cat technicalFindExample.txt
```

Here we only look at the directory itself and at the *first-level* subdirectories, getting the answer:
```
technical
technical/government
technical/plos
technical/biomed
technical/911report
```
So we have 4 subdirectories of `./technical` and `technical` itself
We could use it to immediately see how many files and subdirs are in a direcotry.

Things get more interesting if we increase the value to 2.
```
find technical -maxdepth 2 > technicalFindExample.txt
wc technicalFindExample.txt
```
We get the output:
```
1117    1117   39580 technicalFindExample.txt
```
See that `1117` is less than initial `1402`. We may conclude that subdirs `./technical` have their owm subdirectories containing files. This is usefull to make the basic file analysis and understanfding the structure of the directory.

We know about the command line argument `name` from lab on week 4. Lets use it in our analysis of `maxdepth`.

Command:

```
find technical -maxdepth 2 -name "*.txt" > technicalFindExample.txt 
wc technicalFindExample.txt 
```

Produces output:
```
1106    1106   39290 technicalFindExample.txt
```
So we know that there are 1106 files of the `txt` type in the first 2 levels of `./technical`. By this example I wanted to show, that we can combine two command line arguments together to have more robust scripts.

## Command line argument `-size`

Before showing how I can use `-size` lets find out how many `.txt` files are in the `./technical` directory and its subdirs.

Easily we get 1391 `.txt` files:

```
find technical -name "*.txt" > technicalFindExample.txt
wc technicalFindExample.txt

1391    1391   54178 technicalFindExample.txt
```

Lets find how many `.txt` files are more than 40 kilobytes in size. It is very usefull for finding large files in case we want to be space efficient.

```
find technical -name "*.txt" -size +40k > technicalFindExample.txt
wc technicalFindExample.txt                                       

206     206    8591 technicalFindExample.txt
```
There are 206 of them

Now lets see how many files are less than 20 kilobytes. This is very useful in case we want to find files we forgot to edit in case we know that each files should contain at least 1000 lines of text.

```
find technical -name "*.txt" -size -20k > technicalFindExample.txt
wc technicalFindExample.txt                                       

572     572   23249 technicalFindExample.txt
```
There are 572 of them


Using the following notation:

-  **(20,40):** number of files with sizes between 20 kB and 40 kB

- **Total:** total number of files

- **40:** number of files more than 40 kB

- **20:** number of files less than 20 kB

Using some basic math we have the following relation:

`(20,40) = Total - 40 - 20`

Providing this command:

```
find technical -name "*.txt" -size +20k -size -40k > technicalFindExample.txt
wc technicalFindExample.txt 

613     613   22338 technicalFindExample.txt
```
There are 613 such files. Lets check is it consistent with the formula I outlined above: `1391 - 206 - 572 = 613` Indeed out formula was correct. This is extremely usefull to find files that have a specific size in some range.

## Command line argument `-delete`

Wa can delete all the files that starts with the letter a. It is useful in case we have each files name corresponding to a book and we want to get rid of all the books starting with letter a. (Maybe we finished them all)

```
find ./technical -name "a*.txt" -delete

find ./technical -type f > technicalFindExample.txt
wc technicalFindExample.txt
1345    1345   55366 technicalFindExample.txt
```

Note the number of files decreased from initial 1391

```
find technical -name "*.txt" -size +40k -delete
find ./technical -type f > technicalFindExample.txt
wc technicalFindExample.txt

 1145    1145   46657 technicalFindExample.txt
```
We can also delete the files that are too large it is usefull in case we want to save the space. Note that the total number of files decreased from previous 1345.

Now we can delete all the txt files. And leave out only the subdirectories. It is useful in case we want to reuse the overall directory strucutry and seed it with new files. 

```
find technical -name "*.txt" -delete             
find technical > technicalFindExample.txt 
cat technicalFindExample.txt 

technical
technical/government
technical/government/About_LSC
technical/government/Env_Prot_Agen
technical/government/Alcohol_Problems
technical/government/Gen_Account_Office
technical/government/Post_Rate_Comm
technical/government/Media
technical/plos
technical/biomed
technical/911report
```
