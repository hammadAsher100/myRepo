Question 1:

Open-ended scenario: University lab submission tracker

Scenario

You are a lab assistant at a university. Every week students drop their assignment files into a shared submissions/ directory. The files are a mix of text reports, data files and shell scripts. The department head wants a single shell script that explores what is in that directory, organises the files into sub-folders by extension, and produces a word-count and line-count breakdown of every text file — all saved into one report.

Sample directory contents

ali\_report.txt   sara\_data.csv   usman\_notes.txt

fatima\_prog.sh   hassan\_data.csv   zara\_report.txt

Tasks

Use pwd and ls -lh to display the current directory and list all files with their sizes. Redirect the output to the report file.

Use mkdir, and a for loop with if / elif to sort every file into text\_files/, data\_files/, or script\_files/ based on its extension. Log each move into the report using >>.

Use wc -l and wc -w on every .txt file and append a formatted summary table to the report. Also use grep to find any file containing the word report and append those results.

End the script by displaying the total file count per sub-folder using ls piped into wc -l, and append a final summary to the report.



Answer 1:

\#!/bin/bash

\# ============================================================

\#  Script   : submission\_tracker.sh

\#  Scenario : University Lab Submission Tracker

\# ============================================================



REPORT="tracker\_report.txt"



echo ""                                                > "$REPORT"

echo "============================================" >> "$REPORT"

echo "     UNIVERSITY SUBMISSION TRACKER REPORT  " >> "$REPORT"

echo "============================================" >> "$REPORT"

echo "Date    : $(date)"                            >> "$REPORT"

echo "User    : $(whoami)"                          >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 1: Explore the directory

\# ------------------------------------------------------------

echo "---- Task 1: Directory Exploration ----"     >> "$REPORT"

echo "Current directory: $(pwd)"                   >> "$REPORT"

echo ""                                             >> "$REPORT"



mkdir -p submissions

touch submissions/ali\_report.txt

touch submissions/sara\_data.csv

touch submissions/usman\_notes.txt

touch submissions/fatima\_prog.sh

touch submissions/hassan\_data.csv

touch submissions/zara\_report.txt



echo "ali notes for the lab report assignment." > submissions/ali\_report.txt

echo "sara,data,values,collected,from,survey."  > submissions/sara\_data.csv

echo "usman notes on operating system concepts." > submissions/usman\_notes.txt

echo "zara report on memory management topic."  > submissions/zara\_report.txt



echo "All files in submissions/:"                >> "$REPORT"

ls -lh submissions/                             >> "$REPORT"

echo ""                                          >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 2: Sort files into sub-folders by extension

\# ------------------------------------------------------------

echo "---- Task 2: Sorting Files by Extension ----" >> "$REPORT"



mkdir -p submissions/text\_files

mkdir -p submissions/data\_files

mkdir -p submissions/script\_files



for file in submissions/\*; do

&#x20;   if \[ -f "$file" ]; then

&#x20;       fname=$(basename "$file")

&#x20;       ext="${fname##\*.}"



&#x20;       if \[ "$ext" = "txt" ]; then

&#x20;           cp "$file" submissions/text\_files/

&#x20;           echo "Sorted to text\_files   : $fname"   >> "$REPORT"

&#x20;       elif \[ "$ext" = "csv" ]; then

&#x20;           cp "$file" submissions/data\_files/

&#x20;           echo "Sorted to data\_files   : $fname"   >> "$REPORT"

&#x20;       elif \[ "$ext" = "sh" ]; then

&#x20;           cp "$file" submissions/script\_files/

&#x20;           echo "Sorted to script\_files : $fname"   >> "$REPORT"

&#x20;       fi

&#x20;   fi

done

echo ""                                              >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 3: wc on .txt files + grep for keyword

\# ------------------------------------------------------------

echo "---- Task 3: Word and Line Count of Text Files ----" >> "$REPORT"

printf "%-28s %8s %8s\\n" "File" "Lines" "Words"           >> "$REPORT"

echo "--------------------------------------------"        >> "$REPORT"



for file in submissions/\*.txt; do

&#x20;   if \[ -f "$file" ]; then

&#x20;       fname=$(basename "$file")

&#x20;       lines=$(wc -l < "$file")

&#x20;       words=$(wc -w < "$file")

&#x20;       printf "%-28s %8s %8s\\n" "$fname" "$lines" "$words" >> "$REPORT"

&#x20;   fi

done

echo ""                                                     >> "$REPORT"



echo "Files containing the word 'report' (grep):"          >> "$REPORT"

grep -rl "report" submissions/\*.txt                        >> "$REPORT"

echo ""                                                     >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 4: Count per sub-folder + final summary

\# ------------------------------------------------------------

echo "---- Task 4: Files Per Sub-folder ----"     >> "$REPORT"

echo "text\_files/   : $(ls submissions/text\_files/   | wc -l) file(s)" >> "$REPORT"

echo "data\_files/   : $(ls submissions/data\_files/   | wc -l) file(s)" >> "$REPORT"

echo "script\_files/ : $(ls submissions/script\_files/ | wc -l) file(s)" >> "$REPORT"

echo ""                                            >> "$REPORT"



echo "Sorted full submission listing:"            >> "$REPORT"

ls submissions/ | grep -v "/" | sort              >> "$REPORT"

echo ""                                            >> "$REPORT"



echo "============================================" >> "$REPORT"

echo "             END OF REPORT                 " >> "$REPORT"

echo "============================================" >> "$REPORT"



echo "Report saved to: $REPORT"

cat "$REPORT"





Question 2:

Open-ended scenario: System log analyser

Scenario

A system administrator manages a Linux server that produces three log files daily. Each line in a log file starts with a severity level: INFO, WARNING, or ERROR. The admin needs a script that reads all three logs, counts each severity type per file, collects all error lines across every file, sorts them alphabetically, and writes a concise monitoring report.

Sample log line format

INFO   User login successful

WARNING Disk usage high

ERROR   Connection failed

Tasks

Use ls and wc -l to list all log files and display the total line count of each. Redirect this to the report.

Use a while loop to iterate over each log file. Inside the loop use grep -c to count INFO, WARNING and ERROR lines, and append the per-file counts using >>.

Use grep combined with a pipe into sort to collect and alphabetically sort all ERROR lines from all three files in a single command. Append the sorted list to the report.

Use an if condition with the -gt operator to check if total errors exceed a threshold and print a CRITICAL or OK status line at the bottom of the report.

Answer 2:

\#!/bin/bash

\# ============================================================

\#  Script   : log\_analyser.sh

\#  Scenario : System Log Analyser

\# ============================================================



REPORT="log\_report.txt"

THRESHOLD=5



echo ""                                                > "$REPORT"

echo "============================================" >> "$REPORT"

echo "        SYSTEM LOG ANALYSER REPORT         " >> "$REPORT"

echo "============================================" >> "$REPORT"

echo "Date      : $(date)"                          >> "$REPORT"

echo "Generated : $(whoami)"                        >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ---- Create 3 sample log files ----

mkdir -p logs



cat > logs/system.log << 'EOF'

INFO User login successful

WARNING Disk usage high

ERROR File not found

INFO System update complete

ERROR Connection failed

INFO Backup completed

WARNING Memory usage at 85%

ERROR Service crashed

INFO New user created

WARNING CPU temperature high

EOF



cat > logs/network.log << 'EOF'

INFO Network interface up

ERROR Packet loss detected

WARNING Bandwidth limit reached

INFO DNS resolved successfully

ERROR Firewall rule failed

INFO VPN connected

WARNING Latency above threshold

ERROR Port scan detected

INFO SSH connection established

WARNING Unused port open

EOF



cat > logs/application.log << 'EOF'

INFO Application started

ERROR Database connection failed

WARNING Session timeout approaching

INFO User authenticated

ERROR Null pointer exception

INFO Cache cleared

WARNING Deprecated API used

ERROR Unhandled exception thrown

INFO Scheduled task completed

WARNING Low disk space for logs

EOF



\# ------------------------------------------------------------

\# TASK 1: List log files and total line count of each

\# ------------------------------------------------------------

echo "---- Task 1: Log Files and Line Counts ----" >> "$REPORT"

echo "Log files found:"                            >> "$REPORT"

ls -lh logs/                                      >> "$REPORT"

echo ""                                            >> "$REPORT"



echo "Total lines per log file:"                  >> "$REPORT"

for logfile in logs/\*.log; do

&#x20;   fname=$(basename "$logfile")

&#x20;   echo "$fname : $(wc -l < "$logfile") lines"  >> "$REPORT"

done

echo ""                                            >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 2: while loop — per-file severity counts

\# ------------------------------------------------------------

echo "---- Task 2: Severity Counts Per File ----" >> "$REPORT"



total\_errors=0



for logfile in logs/\*.log; do

&#x20;   fname=$(basename "$logfile")

&#x20;   info=$(grep -c "^INFO"    "$logfile")

&#x20;   warn=$(grep -c "^WARNING" "$logfile")

&#x20;   err=$(grep -c  "^ERROR"   "$logfile")

&#x20;   total\_errors=$((total\_errors + err))



&#x20;   echo "\[ $fname ]"                             >> "$REPORT"

&#x20;   echo "  INFO    : $info"                      >> "$REPORT"

&#x20;   echo "  WARNING : $warn"                      >> "$REPORT"

&#x20;   echo "  ERROR   : $err"                       >> "$REPORT"

&#x20;   echo ""                                        >> "$REPORT"

done



\# ------------------------------------------------------------

\# TASK 3: All ERROR lines sorted via pipe

\# ------------------------------------------------------------

echo "---- Task 3: All ERROR Lines Sorted (grep | sort) ----" >> "$REPORT"

grep -h "^ERROR" logs/\*.log | sort                            >> "$REPORT"

echo ""                                                        >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 4: if -gt threshold → CRITICAL or OK

\# ------------------------------------------------------------

echo "---- Task 4: Overall Status ----"          >> "$REPORT"

echo "Total ERROR count across all files: $total\_errors" >> "$REPORT"

echo "Threshold set to: $THRESHOLD"              >> "$REPORT"

echo ""                                           >> "$REPORT"



if \[ $total\_errors -gt $THRESHOLD ]; then

&#x20;   echo "STATUS: CRITICAL — error count exceeds threshold!" >> "$REPORT"

else

&#x20;   echo "STATUS: OK — system within acceptable limits."     >> "$REPORT"

fi

echo ""                                           >> "$REPORT"



echo "============================================" >> "$REPORT"

echo "             END OF REPORT                 " >> "$REPORT"

echo "============================================" >> "$REPORT"



echo "Report saved to: $REPORT"

cat "$REPORT"





Question 3:

Open-ended scenario: Student grade checker

Scenario

An instructor stores each student's marks in a plain text file, one mark per line. At the end of the semester the instructor wants a script that reads each file, computes a simple pass/fail decision, counts how many students passed and failed, and appends a grade report — without writing a single C program.

Sample marks file format

Student: Ali Ahmed

Marks: 72

Student: Sara Khan

Marks: 41

Tasks

Use touch to create at least four student mark files, then use echo with > to write the student name and marks into each file.

Use a for loop to iterate over all mark files. Inside the loop use grep to extract the numeric mark and store it in a variable using read.

Use an if / else with the -ge operator (pass mark = 50) to label each student PASS or FAIL. Append the result for every student to the report using >>.

After the loop use echo to append a summary showing total students, total passed and total failed. Also sort the report lines alphabetically and append the sorted version as a final section.

Answer 3:

\#!/bin/bash

\# ============================================================

\#  Script   : grade\_checker.sh

\#  Scenario : Student Grade Checker

\# ============================================================



REPORT="grade\_report.txt"

PASS\_MARK=50



echo ""                                                > "$REPORT"

echo "============================================" >> "$REPORT"

echo "        STUDENT GRADE CHECKER REPORT       " >> "$REPORT"

echo "============================================" >> "$REPORT"

echo "Date       : $(date)"                         >> "$REPORT"

echo "Pass mark  : $PASS\_MARK"                      >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 1: Create student mark files using touch and echo >

\# ------------------------------------------------------------

mkdir -p marks



touch marks/ali\_ahmed.txt

touch marks/sara\_khan.txt

touch marks/usman\_ali.txt

touch marks/fatima\_malik.txt

touch marks/hassan\_raza.txt



echo "Student: Ali Ahmed"    > marks/ali\_ahmed.txt

echo "Marks: 72"            >> marks/ali\_ahmed.txt



echo "Student: Sara Khan"    > marks/sara\_khan.txt

echo "Marks: 41"            >> marks/sara\_khan.txt



echo "Student: Usman Ali"    > marks/usman\_ali.txt

echo "Marks: 85"            >> marks/usman\_ali.txt



echo "Student: Fatima Malik" > marks/fatima\_malik.txt

echo "Marks: 49"            >> marks/fatima\_malik.txt



echo "Student: Hassan Raza"  > marks/hassan\_raza.txt

echo "Marks: 61"            >> marks/hassan\_raza.txt



echo "---- Task 1: Mark Files Created ----"        >> "$REPORT"

ls -lh marks/                                      >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 2 + 3: for loop, grep to extract mark, if -ge for pass/fail

\# ------------------------------------------------------------

echo "---- Task 2 \& 3: Grade Results ----"         >> "$REPORT"

printf "%-22s %6s %10s\\n" "Student" "Marks" "Result" >> "$REPORT"

echo "--------------------------------------------" >> "$REPORT"



passed=0

failed=0



for file in marks/\*.txt; do

&#x20;   student=$(grep "^Student:" "$file" | cut -d' ' -f2-)

&#x20;   marks=$(grep "^Marks:" "$file" | cut -d' ' -f2)



&#x20;   if \[ "$marks" -ge "$PASS\_MARK" ]; then

&#x20;       result="PASS"

&#x20;       passed=$((passed + 1))

&#x20;   else

&#x20;       result="FAIL"

&#x20;       failed=$((failed + 1))

&#x20;   fi



&#x20;   printf "%-22s %6s %10s\\n" "$student" "$marks" "$result" >> "$REPORT"

done

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 4: Summary + sorted listing

\# ------------------------------------------------------------

echo "---- Task 4: Summary ----"                   >> "$REPORT"

total=$((passed + failed))

echo "Total students : $total"                     >> "$REPORT"

echo "Passed         : $passed"                    >> "$REPORT"

echo "Failed         : $failed"                    >> "$REPORT"

echo ""                                             >> "$REPORT"



echo "Sorted mark files (ls | sort):"             >> "$REPORT"

ls marks/ | sort                                   >> "$REPORT"

echo ""                                             >> "$REPORT"



echo "============================================" >> "$REPORT"

echo "             END OF REPORT                 " >> "$REPORT"

echo "============================================" >> "$REPORT"



echo "Report saved to: $REPORT"

cat "$REPORT"



Question 4:

Open-ended scenario: Backup file verifier

Labs 2, 4, 5

Scenario

A database admin runs nightly backups that produce one text file per database. Sometimes a backup silently fails and produces an empty file. The admin needs a shell script that checks every backup file, flags empty ones as FAILED, counts words in the healthy ones, and generates a verification report the manager can review each morning.

Sample backup directory

db\_orders\_backup.txt   — 59 bytes (ok)

db\_users\_backup.txt    — 55 bytes (ok)

db\_logs\_backup.txt     — 0 bytes  (empty!)

Tasks

Use mkdir and touch to create a backups/ directory with at least four backup files. Write content into three of them using echo >; leave one empty to simulate a failure.

Use a for loop over all .txt files. Inside the loop use wc -c to get the byte size and an if / else with the -gt operator to decide PASSED or FAILED. Append each result using >>.

For every PASSED file, use wc -w and wc -l to append a word and line count. Use grep to check if the word successfully appears and note it in the report.

Use ls piped into sort to append a sorted backup file listing at the end. Finish with an if condition that prints a WARNING if any failures were found or ALL CLEAR otherwise.

Answer 4:

\#!/bin/bash

\# ============================================================

\#  Script   : backup\_verifier.sh

\#  Scenario : Database Backup File Verifier

\# ============================================================



REPORT="backup\_report.txt"



echo ""                                                > "$REPORT"

echo "============================================" >> "$REPORT"

echo "       DATABASE BACKUP VERIFIER REPORT     " >> "$REPORT"

echo "============================================" >> "$REPORT"

echo "Date    : $(date)"                            >> "$REPORT"

echo "User    : $(whoami)"                          >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 1: Create backup directory + files

\# ------------------------------------------------------------

mkdir -p backups



echo "db\_orders backup: 1500 records exported successfully." > backups/db\_orders\_backup.txt

echo "db\_users backup: 320 users exported successfully."     > backups/db\_users\_backup.txt

echo "db\_products backup: 870 products exported."            > backups/db\_products\_backup.txt

touch backups/db\_logs\_backup.txt



echo "---- Task 1: Backup Files Found ----"        >> "$REPORT"

ls -lh backups/                                    >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 2: for loop + wc -c + if -gt → PASSED / FAILED

\# ------------------------------------------------------------

echo "---- Task 2: Backup Status Check ----"       >> "$REPORT"



passed=0

failed=0



for file in backups/\*.txt; do

&#x20;   fname=$(basename "$file")

&#x20;   size=$(wc -c < "$file")



&#x20;   if \[ "$size" -gt 0 ]; then

&#x20;       echo "PASSED : $fname ($size bytes)"       >> "$REPORT"

&#x20;       passed=$((passed + 1))

&#x20;   else

&#x20;       echo "FAILED : $fname — file is empty"     >> "$REPORT"

&#x20;       failed=$((failed + 1))

&#x20;   fi

done

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 3: wc -w / wc -l on passed files + grep for "successfully"

\# ------------------------------------------------------------

echo "---- Task 3: Content Analysis of Passed Backups ----" >> "$REPORT"

printf "%-32s %8s %8s\\n" "File" "Lines" "Words"            >> "$REPORT"

echo "--------------------------------------------"          >> "$REPORT"



for file in backups/\*.txt; do

&#x20;   fname=$(basename "$file")

&#x20;   size=$(wc -c < "$file")



&#x20;   if \[ "$size" -gt 0 ]; then

&#x20;       lines=$(wc -l < "$file")

&#x20;       words=$(wc -w < "$file")

&#x20;       printf "%-32s %8s %8s\\n" "$fname" "$lines" "$words" >> "$REPORT"



&#x20;       if grep -q "successfully" "$file"; then

&#x20;           echo "  >> contains 'successfully' — confirmed ok" >> "$REPORT"

&#x20;       fi

&#x20;   fi

done

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 4: Sorted listing + if WARNING / ALL CLEAR

\# ------------------------------------------------------------

echo "---- Task 4: Sorted Backup Listing (ls | sort) ----" >> "$REPORT"

ls backups/ | sort                                         >> "$REPORT"

echo ""                                                     >> "$REPORT"



echo "---- Final Status ----"                     >> "$REPORT"

echo "Total  : $((passed + failed))"              >> "$REPORT"

echo "Passed : $passed"                           >> "$REPORT"

echo "Failed : $failed"                           >> "$REPORT"

echo ""                                            >> "$REPORT"



if \[ "$failed" -gt 0 ]; then

&#x20;   echo "STATUS: WARNING — $failed backup(s) failed. Investigate immediately." >> "$REPORT"

else

&#x20;   echo "STATUS: ALL CLEAR — all backups verified."  >> "$REPORT"

fi

echo ""                                            >> "$REPORT"



echo "============================================" >> "$REPORT"

echo "             END OF REPORT                 " >> "$REPORT"

echo "============================================" >> "$REPORT"



echo "Report saved to: $REPORT"

cat "$REPORT"





Question 5:

Open-ended scenario: Directory change tracker

Scenario

A junior sysadmin needs to document the state of three project directories for a handover report. The script must visit each directory, display who is logged in, record what files exist, search for any file containing a specific keyword, and produce a single handover document that the incoming admin can read at a glance.

Directory structure

project\_a/   project\_b/   project\_c/

(each contains a mix of .txt and .sh files)

Tasks

Use whoami, uname, and cal to record system identity and the current calendar at the top of the report using > and >>.

Use mkdir and touch to create three project directories each containing at least two files. Then use a for loop to visit each directory, run pwd and ls inside it, and append the output to the report.

Inside the same loop use find . -iname to search for all .txt files in each project directory and append the found paths to the report.

After the loop use grep piped into sort to search all .txt files for a keyword entered by the user via read, and append the sorted matches. End with a total file count using ls piped into wc -l.

Answer 5:

\#!/bin/bash

\# ============================================================

\#  Script   : dir\_tracker.sh

\#  Scenario : Directory Change Tracker / Handover Report

\# ============================================================



REPORT="handover\_report.txt"



echo ""                                                > "$REPORT"

echo "============================================" >> "$REPORT"

echo "         DIRECTORY HANDOVER REPORT         " >> "$REPORT"

echo "============================================" >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 1: whoami, uname, cal at report header

\# ------------------------------------------------------------

echo "Prepared by : $(whoami)"                     >> "$REPORT"

echo "System      : $(uname -n)"                   >> "$REPORT"

echo "OS          : $(uname -o)"                   >> "$REPORT"

echo ""                                             >> "$REPORT"

echo "---- Calendar ----"                           >> "$REPORT"

cal                                                >> "$REPORT"

echo ""                                             >> "$REPORT"



\# ------------------------------------------------------------

\# TASK 2: Create 3 project directories with files,

\#         then for loop: pwd equivalent + ls per dir

\# ------------------------------------------------------------

mkdir -p project\_a project\_b project\_c



touch project\_a/requirements.txt  project\_a/setup.sh

touch project\_b/design\_notes.txt  project\_b/deploy.sh

touch project\_c/test\_results.txt  project\_c/run.sh



echo "project overview for phase one."   > project\_a/requirements.txt

echo "design notes covering all modules." > project\_b/design\_notes.txt

echo "test results show all cases passed." > project\_c/test\_results.txt



echo "---- Task 2: Directory Listings ----"        >> "$REPORT"



for dir in project\_a project\_b project\_c; do

&#x20;   echo "\[ $dir/ ] — path: $(pwd)/$dir"           >> "$REPORT"

&#x20;   ls -lh "$dir/"                                 >> "$REPORT"

&#x20;   echo ""                                         >> "$REPORT"

done



\# ------------------------------------------------------------

\# TASK 3: find -iname inside each dir

\# ------------------------------------------------------------

echo "---- Task 3: Text Files Found (find -iname) ----" >> "$REPORT"



for dir in project\_a project\_b project\_c; do

&#x20;   echo "Searching in $dir/:"                    >> "$REPORT"

&#x20;   find "$dir" -iname "\*.txt"                    >> "$REPORT"

&#x20;   echo ""                                        >> "$REPORT"

done



\# ------------------------------------------------------------

\# TASK 4: read keyword, grep | sort across all .txt, total count

\# ------------------------------------------------------------

echo "Enter a keyword to search across all project text files:"

read keyword



echo "---- Task 4: Keyword Search — '$keyword' (grep | sort) ----" >> "$REPORT"

grep -rl "$keyword" project\_a/\*.txt project\_b/\*.txt project\_c/\*.txt 2>/dev/null | sort >> "$REPORT"

echo ""                                                               >> "$REPORT"



echo "Matching lines containing '$keyword':"      >> "$REPORT"

grep -h "$keyword" project\_a/\*.txt project\_b/\*.txt project\_c/\*.txt 2>/dev/null | sort >> "$REPORT"

echo ""                                            >> "$REPORT"



echo "---- Total file count per directory ----"   >> "$REPORT"

for dir in project\_a project\_b project\_c; do

&#x20;   count=$(ls "$dir/" | wc -l)

&#x20;   echo "$dir/ : $count file(s)"                 >> "$REPORT"

done

echo ""                                            >> "$REPORT"



echo "Overall total files across all projects:"   >> "$REPORT"

ls project\_a/ project\_b/ project\_c/ | wc -l      >> "$REPORT"

echo ""                                            >> "$REPORT"



echo "============================================" >> "$REPORT"

echo "             END OF REPORT                 " >> "$REPORT"

echo "============================================" >> "$REPORT"



echo "Report saved to: $REPORT"

cat "$REPORT"



==============Section B Oel=========================

Open Ended Lab Scenario: System Log Monitoring Tool



Scenario



A system administrator manages a Linux server that produces multiple log files containing system messages, warnings, and errors such as:

INFO User login successful  

WARNING Disk usage high  

ERROR File not found  

INFO System update complete  

ERROR Connection failed  



The administrator wants a shell script that automatically scans all log files and generates a summary report for overall system analysis.



Requirements

Create a shell based monitoring tool that:

* Analyzes at least 3 log files
* Uses Linux commands to process logs
* Generates a report file containing:
* Date and time of report generation
* Log file names
* Total number of entries
* Count of:

ERROR messages

WARNING messages

INFO messages

Displays useful statistics for analysis

Saves the final output into a report file



ANSWER:

\#!/bin/bash



\# Report file

REPORT="log\_report.txt"



\# Clear previous report

echo "System Log Monitoring Report" > $REPORT

echo "Generated on: $(date)" >> $REPORT

echo "--------------------------------------" >> $REPORT



\# Log files list

LOG\_FILES=("log1.txt" "log2.txt" "log3.txt")



TOTAL\_ENTRIES=0

TOTAL\_ERRORS=0

TOTAL\_WARNINGS=0

TOTAL\_INFO=0



\# Loop through log files

for FILE in "${LOG\_FILES\[@]}"

do

&#x20;   if \[ -f "$FILE" ]; then

&#x20;       echo "Processing file: $FILE" >> $REPORT



&#x20;       ENTRIES=$(wc -l < "$FILE")

&#x20;       ERRORS=$(grep -c "ERROR" "$FILE")

&#x20;       WARNINGS=$(grep -c "WARNING" "$FILE")

&#x20;       INFO=$(grep -c "INFO" "$FILE")



&#x20;       echo "Total Entries: $ENTRIES" >> $REPORT

&#x20;       echo "Errors: $ERRORS" >> $REPORT

&#x20;       echo "Warnings: $WARNINGS" >> $REPORT

&#x20;       echo "Info: $INFO" >> $REPORT

&#x20;       echo "--------------------------------------" >> $REPORT



&#x20;       # Add to totals

&#x20;       TOTAL\_ENTRIES=$((TOTAL\_ENTRIES + ENTRIES))

&#x20;       TOTAL\_ERRORS=$((TOTAL\_ERRORS + ERRORS))

&#x20;       TOTAL\_WARNINGS=$((TOTAL\_WARNINGS + WARNINGS))

&#x20;       TOTAL\_INFO=$((TOTAL\_INFO + INFO))

&#x20;   else

&#x20;       echo "File $FILE not found" >> $REPORT

&#x20;   fi

done



\# Final summary

echo "Overall Summary" >> $REPORT

echo "Total Entries: $TOTAL\_ENTRIES" >> $REPORT

echo "Total Errors: $TOTAL\_ERRORS" >> $REPORT

echo "Total Warnings: $TOTAL\_WARNINGS" >> $REPORT

echo "Total Info: $TOTAL\_INFO" >> $REPORT



echo "Report generated successfully in $REPORT"





==============Section A Oel=========================

Open-Ended Scenario: Automated File Organizer



You are working as a system assistant in a university computer lab. Every week, students submit many files such as text reports, program files, and data files in a directory called submissions.



Because the number of files is large, the instructor wants a system that can:

* Automatically check files
* Organize files
* Analyze files



You are asked to design a solution using shell scripting and file commands.



Tasks

1. File exploration using commands
2. Conditional shell scripting
3. File analysis



The script should generate a simple report file that shows all the analysis performed.



ANSWER:
#!/bin/bash

DIR="submissions"
REPORT="report.txt"

echo "File Analysis Report" > $REPORT

# check directory
if [ ! -d "$DIR" ]; then
    echo "Folder not found"
    exit 1
fi

txt=0
c=0
csv=0

# list files
echo "Files:" >> $REPORT
ls $DIR >> $REPORT
echo "" >> $REPORT

# text files
for f in $DIR/*.txt
do
    [ -f "$f" ] || continue
    txt=$((txt+1))
    echo "$f lines: $(wc -l < $f)" >> $REPORT
done

# c files
for f in $DIR/*.c
do
    [ -f "$f" ] || continue
    c=$((c+1))
    echo "$f lines: $(wc -l < $f)" >> $REPORT
done

# csv files
for f in $DIR/*.csv
do
    [ -f "$f" ] || continue
    csv=$((csv+1))
    echo "$f rows: $(wc -l < $f)" >> $REPORT
done

# summary
echo "" >> $REPORT
echo "Summary:" >> $REPORT
echo "txt: $txt" >> $REPORT
echo "c: $c" >> $REPORT
echo "csv: $csv" >> $REPORT

echo "Done. Check report.txt"


=================Commands=============================

════════════════════════════════════════════════════════

&#x20; SECTION 1: SHELL COMMANDS

════════════════════════════════════════════════════════



────────────────────────────────────────────────────────

&#x20; LAB 1 — Introduction to Linux

────────────────────────────────────────────────────────



ls                          List directory contents

cd <dir>                    Change to the specified directory

pwd                         Print current working directory

mkdir <dir>                 Create a new directory

rmdir <dir>                 Remove an empty directory

rm <file>                   Remove a file

rm -r <dir>                 Remove a directory and all its contents recursively

cp <src> <dst>              Copy a file or directory

mv <src> <dst>              Move or rename a file or directory

cat <file>                  Display the contents of a file

who                         Show all users currently logged in

whoami                      Display the current username

uname                       Display system information

groups                      Show all groups the current user belongs to

cal                         Display a calendar for the current month

sleep <n>                   Pause execution for n seconds

shutdown                    Power off or reboot the machine

gcc -o <output> <file.c>    Compile a C source file into an executable

./<program>                 Execute a compiled binary in the current directory





────────────────────────────────────────────────────────

&#x20; LAB 2 — Linux Commands and Shell Scripting

────────────────────────────────────────────────────────



cp data.txt /var/tmp/               Copy a single file to a destination directory

cp data.txt file.csv /var/tmp/      Copy multiple files to a destination directory

man <command>                        Display the manual page for a command

chmod 755 <file.sh>                 Set executable permissions on a shell script

bash my\_script.sh                   Run a shell script via the bash interpreter

./my\_script.sh                      Execute a shell script directly (after chmod)

echo "Hello World!"                 Print text to standard output

\#!/bin/bash                         Shebang line — declares bash as the interpreter

\# comment                           Inline comment (line is ignored by the shell)





────────────────────────────────────────────────────────

&#x20; LAB 3 — Find, Grep, Sort and Shell Variables

────────────────────────────────────────────────────────



&#x20; find Command:

&#x20;   Syntax:   find \[options] \[starting/path] \[expression]

&#x20;   $ find . -iname file.txt          Find file.txt (case-insensitive) in current dir

&#x20;   $ find . -iname "\*.c"             Find all .c files (case-insensitive)



&#x20; grep Command:

&#x20;   Syntax:   grep \[pattern] \[file]

&#x20;   $ grep int file.c                 Find all lines containing 'int'

&#x20;   $ grep "int main" file.c          Find exact phrase 'int main'



&#x20; sort Command:

&#x20;   Syntax:   sort \[expression]

&#x20;   $ sort filename.txt               Sort lines (A-Z) and display on screen

&#x20;   $ sort filename.txt > sorted.txt  Sort and write output to sorted.txt

&#x20;   $ sort filename.txt >> sorted.txt Sort and append output to sorted.txt

&#x20;   $ sort -r filename.txt            Sort in reverse (descending) order



&#x20; Shell Variables:

&#x20;   variable\_name=variable\_value      Assign a value to a variable

&#x20;   NAME="Zara Ali"                   Declare a string scalar variable

&#x20;   $NAME  or  ${NAME}               Dereference/use a variable

&#x20;   read var1                         Read user input and store in var1



&#x20; gcc (from shell scripts):

&#x20;   gcc hello.c -o hello              Compile C source into executable 'hello'

&#x20;   ./hello                           Run the compiled executable





────────────────────────────────────────────────────────

&#x20; LAB 4 — File Commands, Redirection \& Conditional Structures

────────────────────────────────────────────────────────



&#x20; touch Command:

&#x20;   $ touch file.txt                  Create an empty .txt file

&#x20;   $ touch file.sh                   Create an empty shell script file

&#x20;   $ touch file.txt                  Update the timestamp of an existing file



&#x20; cat Command:

&#x20;   $ cat file1.txt > file2.txt                         Copy file1 contents into file2

&#x20;   $ cat file1.txt file2.txt file3.txt > file4.txt     Concatenate three files into one



&#x20; read Command:

&#x20;   $ read variable                   Read keyboard input and store in 'variable'

&#x20;   $ echo $variable                  Display the stored value



&#x20; wc Command (word count):

&#x20;   Syntax:   wc \[options] filenames

&#x20;   wc -l <file>                      Count lines

&#x20;   wc -w <file>                      Count words

&#x20;   wc -c <file>                      Count bytes

&#x20;   wc -m <file>                      Count characters

&#x20;   wc -L <file>                      Print length of the longest line

&#x20;   wc -l tecmint.txt                 Example usage



&#x20; Redirection Operators:

&#x20;   $ ls -la > filelist.txt           Redirect stdout to file (overwrite)

&#x20;   $ echo "The End" >> filelist.txt  Append stdout to existing file

&#x20;   $ wc -w < document.txt            Read stdin from file

&#x20;   $ wc -w < document.txt > wordcount.txt    Combine input and output redirection



&#x20; Arithmetic Expansion:

&#x20;   Syntax:   $((EXPRESSION))

&#x20;   $ echo $((2+2))                   Evaluate and print: 4

&#x20;   echo $((first\_num + second\_num))  Addition

&#x20;   echo $((first\_num - second\_num))  Subtraction

&#x20;   echo $((first\_num \* second\_num))  Multiplication

&#x20;   echo $((first\_num / second\_num))  Division

&#x20;   echo $((first\_num % second\_num))  Modulus (remainder)



&#x20; Conditional Operators (used inside \[ ]):

&#x20;   <=  >=  <  >    Comparison operators

&#x20;   ==  !=          Equality and inequality

&#x20;   \&\&              Logical AND

&#x20;   ||              Logical OR



&#x20; if / elif / else Structures:



&#x20;   # Form 1 — Simple if

&#x20;   if \[ condition ]; then

&#x20;       commands

&#x20;   fi



&#x20;   # Form 2 — if / else

&#x20;   if \[ condition ]; then

&#x20;       commands

&#x20;   else

&#x20;       commands

&#x20;   fi



&#x20;   # Form 3 — if / elif / else

&#x20;   if \[ condition ]; then

&#x20;       commands

&#x20;   elif \[ condition ]; then

&#x20;       commands

&#x20;   else

&#x20;       commands

&#x20;   fi





────────────────────────────────────────────────────────

&#x20; LAB 5 — Pipes, Operators and Loop Statements

────────────────────────────────────────────────────────



&#x20; Pipe Operator |:

&#x20;   Syntax:   command\_1 | command\_2 | command\_3 ...

&#x20;   $ ls -l | wc -w                         Count words in ls output

&#x20;   $ ls -l | wc -m                         Count characters in ls output

&#x20;   $ ls -l | wc -l                         Count lines in ls output

&#x20;   $ ls | grep "D" | sort > record.txt     Filter, sort, and save to file

&#x20;   $ ls | grep "s" | sort > record.txt     Filter by 's', sort, and save



&#x20; Relational Operators (numeric, used inside \[ ]):

&#x20;   -eq     Equal to:                \[ $a -eq $b ]

&#x20;   -ne     Not equal to:            \[ $a -ne $b ]

&#x20;   -gt     Greater than:            \[ $a -gt $b ]

&#x20;   -lt     Less than:               \[ $a -lt $b ]

&#x20;   -ge     Greater than or equal:   \[ $a -ge $b ]

&#x20;   -le     Less than or equal:      \[ $a -le $b ]



&#x20; Boolean Operators:

&#x20;   !       Logical NOT:   \[ ! false ]

&#x20;   -o      Logical OR:    \[ $a -lt 20 -o $b -gt 100 ]

&#x20;   -a      Logical AND:   \[ $a -lt 20 -a $b -gt 100 ]



&#x20; String Operators:

&#x20;   =       Equal to:      \[ "$a" = "$b" ]

&#x20;   !=      Not equal to:  \[ "$a" != "$b" ]



&#x20; for Loop:



&#x20;   # C-style for loop

&#x20;   for (( EXP1; EXP2; EXP3 ))

&#x20;   do

&#x20;       command1

&#x20;       command2

&#x20;   done



&#x20;   # Example:

&#x20;   for (( i=1; i<=5; i++ ))

&#x20;   do

&#x20;       echo "Welcome $i times"

&#x20;   done



&#x20;   # Range-based for loop

&#x20;   for i in 1 2 3 4 5

&#x20;   do

&#x20;       echo "Welcome $i times"

&#x20;   done



&#x20;   # Step increment  {START..END..INCREMENT}

&#x20;   for i in {0..10..2}

&#x20;   do

&#x20;       echo "Welcome $i times"

&#x20;   done



&#x20;   # Iterating over files

&#x20;   count=0

&#x20;   for file in /home/user/Documents/\*;

&#x20;   do

&#x20;       count=$(( count + 1))

&#x20;   done

&#x20;   echo "Total number of files is $count"



&#x20; while Loop:



&#x20;   Syntax:

&#x20;   while \[ condition ]

&#x20;   do

&#x20;       command1

&#x20;       command2

&#x20;   done



&#x20;   # Example:

&#x20;   x=1

&#x20;   while \[ $x -lt 6 ]

&#x20;   do

&#x20;       echo "Welcome $x times"

&#x20;       x=$(( x + 1 ))

&#x20;   done





════════════════════════════════════════════════════════

&#x20; SECTION 2: SYSTEM CALLS

════════════════════════════════════════════════════════



&#x20; Required Headers:

&#x20;   #include <stdio.h>

&#x20;   #include <sys/types.h>    // pid\_t type definition

&#x20;   #include <unistd.h>       // fork(), getpid(), getppid(), execlp()

&#x20;   #include <stdlib.h>       // exit()



────────────────────────────────────────────────────────

&#x20; Process-Related System Calls

────────────────────────────────────────────────────────



&#x20; fork()

&#x20;   Category:   Process Creation

&#x20;   Syntax:     pid\_t pid = fork();

&#x20;   Description: Creates a new child process by duplicating the calling process.

&#x20;   Return values:

&#x20;      -1   →  Fork failed (error)

&#x20;       0   →  Returned to the CHILD process

&#x20;      >0   →  Returned to the PARENT process (value = child's PID)



&#x20; exit()

&#x20;   Category:   Process Termination

&#x20;   Syntax:     exit(-1);

&#x20;   Description: Terminates the calling process. Non-zero argument indicates error.



&#x20; getpid()

&#x20;   Category:   Process Identification

&#x20;   Syntax:     pid\_t pid = getpid();

&#x20;   Description: Returns the process ID (PID) of the current calling process.



&#x20; getppid()

&#x20;   Category:   Process Identification

&#x20;   Syntax:     pid\_t ppid = getppid();

&#x20;   Description: Returns the PID of the parent of the calling process.



&#x20; execve()

&#x20;   Category:   Program Execution

&#x20;   Syntax:     execve(pathname, argv, envp);

&#x20;   Description: Replaces the current process image with a new program.

&#x20;                All exec() variants are wrappers around this call.



&#x20; execlp()

&#x20;   Category:   Program Execution

&#x20;   Syntax:     execlp(const char \*file, const char \*arg, ..., NULL);

&#x20;   Example:    execlp("/bin/ls", "ls", NULL);

&#x20;   Description: Executes a program, searching PATH for the file. Replaces the

&#x20;                calling process image. Last argument must always be NULL.



&#x20; setuid()

&#x20;   Category:   Identity Management

&#x20;   Syntax:     setuid(uid\_t uid);

&#x20;   Description: Sets the user identity (UID) of the current process.



&#x20; ptrace()

&#x20;   Category:   Debugging / Tracing

&#x20;   Syntax:     ptrace(request, pid, addr, data);

&#x20;   Description: Allows a parent process to observe and control execution of

&#x20;                another process. Used by debuggers (e.g., gdb).



&#x20; wait()

&#x20;   Category:   Process Synchronization

&#x20;   Syntax:     pid\_t pid = wait(int \*status);

&#x20;   Description: Suspends the calling process until one of its child processes

&#x20;                terminates.



&#x20; fprintf(stderr, ...)

&#x20;   Category:   Error Reporting

&#x20;   Syntax:     fprintf(stderr, "Fork failed!\\n");

&#x20;   Description: Writes a formatted error message to standard error output.

&#x20;                Used to report failures such as a failed fork().



────────────────────────────────────────────────────────

&#x20; fork() Logic Template

────────────────────────────────────────────────────────



&#x20;   pid\_t pid;

&#x20;   pid = fork();



&#x20;   if (pid < 0) {

&#x20;       // Fork failed — handle error

&#x20;       fprintf(stderr, "Fork failed!\\n");

&#x20;       exit(-1);

&#x20;   } else if (pid == 0) {

&#x20;       // Child process code

&#x20;       printf("I am the child, PID = %d\\n", getpid());

&#x20;   } else {

&#x20;       // Parent process code

&#x20;       printf("I am the parent, child PID = %d\\n", pid);

&#x20;   }



────────────────────────────────────────────────────────

&#x20; getpid() / getppid() Example

────────────────────────────────────────────────────────



&#x20;   pid = fork();

&#x20;   if (pid == 0) {

&#x20;       printf("Child PID: %d, Parent PID: %d\\n", getpid(), getppid());

&#x20;   } else if (pid > 0) {

&#x20;       printf("Parent PID: %d\\n", getpid());

&#x20;   }



────────────────────────────────────────────────────────

&#x20; execlp() Example (Lab 6 — Task 3)

────────────────────────────────────────────────────────



&#x20;   pid = fork();

&#x20;   if (pid == 0) {

&#x20;       execlp("/bin/ls", "ls", NULL);   // Child replaces itself with 'ls'

&#x20;   } else {

&#x20;       printf("Parent, child PID = %d\\n", pid);

&#x20;   }



====================================================================================================================================================
Open-ended lab scenario: Directory explorer and file organizer
Scenario
A junior Linux administrator has just been given access to a new server. They need to explore the file system, understand what directories exist, and organize files from a messy home directory by copying and moving them into structured folders.
Example file system state
/home/user/report.txt
/home/user/data.csv
/home/user/notes.txt
/home/user/old_backup.txt
Requirements
Use pwd, ls, and whoami to display current location and user identity
Use mkdir to create at least three organized sub-directories
Use cp and mv to distribute files into the appropriate sub-directories
Use rm to delete any unnecessary files after organizing
Use uname to record system information and redirect it into a summary report file using >
The script should create a report file at the end

#!/bin/bash
# ============================================================
#  Script : organizer.sh
#  Scenario: Directory Explorer and File Organizer
#  Lab     : Labs 1 & 2
# ============================================================

REPORT="organizer_report.txt"

# ---- Create/clear report file ----
echo "" > "$REPORT"

echo "============================================" >> "$REPORT"
echo "   DIRECTORY EXPLORER & FILE ORGANIZER     " >> "$REPORT"
echo "============================================" >> "$REPORT"
echo "Report generated on: $(date)"                >> "$REPORT"
echo ""                                             >> "$REPORT"

# ---- System Information ----
echo "---- System Information ----"               >> "$REPORT"
echo "Current User   : $(whoami)"                 >> "$REPORT"
echo "System Info    : $(uname -a)"               >> "$REPORT"
echo "Working Dir    : $(pwd)"                    >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- Create sample files to organize ----
touch report.txt data.csv notes.txt old_backup.txt script.sh image.png
echo "---- Creating sample files ----"            >> "$REPORT"
echo "Files created: report.txt  data.csv  notes.txt  old_backup.txt  script.sh  image.png" >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- List files before organizing ----
echo "---- Files BEFORE organizing ----"          >> "$REPORT"
ls -lh                                            >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- Create organized sub-directories ----
mkdir -p docs data scripts images
echo "---- Creating sub-directories ----"         >> "$REPORT"
echo "Created: docs/   data/   scripts/   images/" >> "$REPORT"
echo ""                                             >> "$REPORT"

# ---- Copy and Move files into directories ----
echo "---- Organizing files ----"                 >> "$REPORT"
cp report.txt docs/
echo "Copied  : report.txt     --> docs/"         >> "$REPORT"

mv notes.txt docs/
echo "Moved   : notes.txt      --> docs/"         >> "$REPORT"

mv data.csv data/
echo "Moved   : data.csv       --> data/"         >> "$REPORT"

mv script.sh scripts/
echo "Moved   : script.sh      --> scripts/"      >> "$REPORT"

mv image.png images/
echo "Moved   : image.png      --> images/"       >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- Delete unnecessary file ----
rm old_backup.txt
echo "---- Removing unnecessary file ----"        >> "$REPORT"
echo "Deleted : old_backup.txt"                   >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- List files AFTER organizing ----
echo "---- Directory structure AFTER organizing ----" >> "$REPORT"
for dir in docs data scripts images; do
    echo "[$dir/]"    >> "$REPORT"
    ls "$dir/"        >> "$REPORT"
done
echo ""               >> "$REPORT"

echo "============================================" >> "$REPORT"

echo "Report saved to: $REPORT"
cat "$REPORT"
========================================================================================================================================================



=======================================================================================================================================================
Open-ended lab scenario: Source code search and sort tool
Scenario
A software team maintains a large collection of C source files across a project directory. A lead developer needs a shell script that automatically searches for all C files, scans them for specific keywords, and produces a sorted summary report.
Example project directory
main.c     — contains: int, printf, return
utils.c    — contains: int, malloc, free
config.c   — contains: define, char, return
Requirements
Use find . -iname "*.c" to locate all C source files in the project directory
Use grep to search for at least two keywords (e.g. int main, printf) across all found files
Use sort to sort the grep results and store them in a report using >>
Include the date, total file count, and keyword match lines in the final report
The script should create a report file at the end

#!/bin/bash
# ============================================================
#  Script : search_sort.sh
#  Scenario: Source Code Search and Sort Tool
#  Lab     : Lab 3
# ============================================================

REPORT="search_sort_report.txt"

# ---- Create/clear report file ----
echo "" > "$REPORT"

echo "============================================" >> "$REPORT"
echo "    SOURCE CODE SEARCH AND SORT TOOL       " >> "$REPORT"
echo "============================================" >> "$REPORT"
echo "Report generated on: $(date)"                >> "$REPORT"
echo ""                                             >> "$REPORT"

# ---- Create sample C source files ----
mkdir -p project

cat > project/main.c << 'EOF'
#include <stdio.h>
int main() {
    printf("Hello from main\n");
    int x = 10;
    return 0;
}
EOF

cat > project/utils.c << 'EOF'
#include <stdio.h>
#include <stdlib.h>
int helper() {
    int val = 0;
    printf("Inside helper\n");
    return val;
}
EOF

cat > project/config.c << 'EOF'
#include <stdio.h>
#define MAX 100
char name[] = "config";
int setup() {
    printf("Setup done\n");
    return 0;
}
EOF

echo "---- Sample C files created ----"           >> "$REPORT"
echo "project/main.c"                             >> "$REPORT"
echo "project/utils.c"                            >> "$REPORT"
echo "project/config.c"                           >> "$REPORT"
echo ""                                            >> "$REPORT"

# ---- Find all .c files ----
echo "---- Finding all .c files (find -iname) ----" >> "$REPORT"
find ./project -iname "*.c"                          >> "$REPORT"
FILE_COUNT=$(find ./project -iname "*.c" | wc -l)
echo "Total .c files found: $FILE_COUNT"             >> "$REPORT"
echo ""                                              >> "$REPORT"

# ---- Search keyword: int main ----
echo "---- grep results for keyword: 'int main' ----" >> "$REPORT"
grep "int main" ./project/*.c | sort                   >> "$REPORT"
echo ""                                                >> "$REPORT"

# ---- Search keyword: printf ----
echo "---- grep results for keyword: 'printf' ----"  >> "$REPORT"
grep "printf" ./project/*.c | sort                    >> "$REPORT"
echo ""                                               >> "$REPORT"

# ---- Search keyword: return ----
echo "---- grep results for keyword: 'return' ----"  >> "$REPORT"
grep "return" ./project/*.c | sort                    >> "$REPORT"
echo ""                                               >> "$REPORT"

# ---- Sorted list of all .c files ----
echo "---- Sorted list of all .c files ----"         >> "$REPORT"
find ./project -iname "*.c" | sort                   >> "$REPORT"
echo ""                                              >> "$REPORT"

echo "============================================"  >> "$REPORT"

echo "Report saved to: $REPORT"
cat "$REPORT"
======================================================================================================================