presentation : https://slides.com/jvbw/deck



"By understanding when and why shark attacks occur, 
it is possible to lessen the likelihood of these incidents." 
This database aims to categorize shark attacks, bringing useful information to visualize patterns of causes and effects.


############### DATA WRANGLING

*************** Importing the file : 
The file wouldn't read properly, returning this error :
"UnicodeDecodeError: 'utf8' codec can't decode byte 0x9c"
 a quick google search gave me an answer: adding "engine = "python"" to the read code.

*************** Quick view of the file :
24 columns and almost 6000 rows.
The columns are : (['Case Number', 'Date', 'Year', 'Type', 'Country', 'Area', 'Location',
       'Activity', 'Name', 'Sex ', 'Age', 'Injury', 'Fatal (Y/N)', 'Time',
       'Species ', 'Investigator or Source', 'pdf', 'href formula', 'href',
       'Case Number.1', 'Case Number.2', 'original order', 'Unnamed: 22',
       'Unnamed: 23'])
Most of the data is of the "object" type, only "Year" and "original order" are int.

*************** Cleaning the column names :
The names were inconsistent and full of blanks, punctuation, lower and upper case...
I cleaned the names to make coding easier and the data iself more legible.

*************** Dropping unnecessary columns :
I decided to drop columns that didn't bring any useful info to the database.
"Name" => the database isn't supposed to be an obituary. Sharks don't descriminate their victims by their names.
"original order" => order the incidents were reported
"investigator or source" => media source. Already cited in the pdf file for each attack.
"pdf" => the pdf is accessible by the link in "href".

*************** Dropping empty columns :
unnamed_22 and unnamed_23 were empty.

*************** Dropping duplicate columns :
"case_number2" and "case_number1" are duplicates of "case number", I dropped them.

*************** Duplicate rows :
There were no complete duplicate rows.

*************** Cleaning text errors :
Homogenizing data, stripping the text from blanks, etc. I looped this over all the columns.

*************** Checking Nans :
Some columns had <10%, some had up to 50% NaNs.
I dropped the rows with NaN values in the columns with <1% NaN, to avoid losing too much data.
# I tried dropping rows using the "thresh" argument in the dropna method, 
# testing if there were rows with more than one "NaN".
# There were no such rows.

*************** Replacing NaNs :
As most of the data is not numerical, I replaced the NaNs with the text "NOT AVAILABLE".

*************** Cleaning the "time" column, grouping times by moments of the day :
I cleaned the "time" column, replacing "h" by ":"
14h30=>14:30
This wasn't really necessary, as I then used a function with regex to group different moments of the day.
The new values are : "afternoon", "morning", "early morning", "late morning", "evening", "night", "early night", "late afternoon","early afternoon"
I also replaced some "AM" by morning, etc.
I dropped the remaining rows.

*************** Cleaning the "activity" column, grouping by activities :
I used a simple text analysis function to group activities by key words : for instance swimming, snorkelling, floating...

*************** Cleaning the age column, grouping by age groups :
Using regex again, I grouped the ages by age groups and dropped the remaining rows.
"under 12", "12-17", "18-24", "25-34","35-44", "45-54", "55-64", "65-74", "over 75".

*************** Injury column :
Contains a lot of info : body part, type of injury, fatality or not...
Analizing the text, I split the column into 2 new columns (body_part_attacked and injury_type)
Mix of regex and text analysis

I then dropped the original column, as the info was divided into the 2 new columns, and fatality was already covered in "fatal_yn"


*************** Reorganizing and renaming columns :
I renamed "TYPE" as "PROVOCATION" as this was the main data contained in this column.
I reorganized the columns in a more intuitive order.

############### DIFFICULTIES AND FAILURES  :

*************** Importing text analysis modules :
Area, country, location :
I wanted to use a geography library, like GEOGRAPY or GEOTEXT to analize the data and group by geographical area.
I couldn't get the modules to work, having trouble with "nltk" that returned a lot of errors.

*************** Time management :
Running out of time, I didn't treat these columns, apart from stripping the text and replacing NaNs.

*************** Deleting data:
Several times, I either mistakingly replaced or delete a big part of my data.
I just need to remember to first store the changes to the df into a temporary df, to test them,
if I'm not sure about my code !

*************** Code length :
Some of my code is long and could certainly be simplified, like the functions I used for my text analysis that have a lot of conditions.


