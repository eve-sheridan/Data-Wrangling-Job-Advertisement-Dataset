# Data-Wrangling-Job-Advertisement-Dataset

## A data wrangling project for a job advertisement dataset by Eve Sheridan

### 1 Introduction
Nowadays there are many job hunting websites including seek.com, Azuna.com, etc. These job hunting sites all manage a job search system, where job hunters could search for relevant jobs based on keywords, salary, and categories, etc.  Job advertisement data analysis is becoming increasingly important and beneficial for job hunting sites, as they can be used to make improvements in the experience of users searching for jobs.

This project uses Python (version 3.7) code to clean and fix problems in a large job advertisement dataset.

### 2 Required format
Each attribute and it's required format are outlined below.

- Id	[Integer] 8 digit Id of the job advertisement
- Title	[String] Title of the advertised job position
- Location	[String] Location of the advertised job position
- Company	[String] Company (employer) of the advertised job position
- ContractType	[String] The contract type of the advertised job position, it could be full_time, part_time or non_specified.
- ContractTime	[String] The contract time of the advertised job position, it could be permanent, contract or non-specified.
- Category	[String] The Category of the advertised job position, e.g., IT jobs, Engineering Jobs, etc.
- Salary	[Float] Annual Salary of the advertised job position, e.g., 80000
- OpenDate	[String] The opening time for applying for the advertised job position, e.g., 20120104T150000, means 3pm, 4th January 2012.
- CloseDate	[String] The closing time for applying for the advertised job position, e.g., 20120104T150000, means 3pm, 4th January 2012.
- SourceName	[String] The website where the job position is advertised.

The "dataset1_with_error.csv" file was initially read into a pandas dataframe (data1) and was composed of 55169 observations and 11 attributes.  All values were changed to uppercase prior to further wrangling.

#### 2.1	Id
There were no null Id values and they were all unique integers of 8 digits long which matched the schema requirements.  No cleaning was required for this attribute.  Id was set as the index for data1.
#### 2.2	ContractType
The attribute ContractType initially contained blank values and hyphens as well as the schema values of FULL_TIME and PART_TIME.  There were 21,676 NaN values.  Any value that wasn’t already assigned to a valid value (including NaN) was remapped to NON_SPECIFIED.  Quite a large number of values in this attribute were assigned the value NON_SPECIFIED (~75%).  
#### 2.3	ContractTime
The attribute ContractTime initially contained blank values and hyphens as well as the schema values of PERMANENT and CONTRACT.  It also had 8,122 NaN values.  Any value that wasn’t already assigned to a valid value (including NaN) was remapped to NON_SPECIFIED.
#### 2.4	Category
Category contained 8 different values and zero NaN values so no cleansing was required on this attribute.
#### 2.5	Source
Source was composed of 106 unique values and zero NaN values.  The majority of the Source values were URL’s but there were some that were not in URL format (e.g. ‘Brand Republic Jobs’.  There were no obvious errors and with no further information about requirements for this field, no data cleansing was performed on this attribute.
#### 2.6	Title
Title was composed of 55,166 unique values prior to cleansing.  A cleaning function was applied to perform the following operations:
-	Normalize to upper case letters
-	Remove special character at the end of the strings
-	Remove special character at the beginning of the strings
-	Replace " AND " with " & " for processing 
-	Remove all special characters except space and dot
-	Change " AND " back to " & " 
-	Replace multiple spaces with a single space, also trim spaces on both side
-	Remove '.' at the end of the strings
-	Remove '.' at the beginning of the strings
There were 55,132 unique values after performing these cleansing operations.
After stripping out all of the special characters, many of the Titles do not make sense.  It was decided to keep these values in order to preserve as much of the data as possible.  These values can easily be filtered out for further analysis if required.  
#### 2.7	Location
There were 489 unique location values in the file prior to cleansing.  Cleansing steps included:
-	Convert to upper case characters
-	Remove any trailing white spaces
There were 484 unique values after cleansing. To further wrangle the location data, the location values should be validated against a database of valid locations.  However this is beyond the scope of this project.

#### 2.8	Company
Initially there were 9065 unique values for attribute Company and 3849 null values (~7% of the file).
##### 2.8.1	Initial cleansing
A cleaning function was applied to perform the following operations:
-	Normalize to upper case letters
-	Remove special character at the end
-	Remove special character at the beginning
-	Replace LIMITED with LTD
-	Replace LIMTED with LTD (misspelt limited)
-	Replace " AND " with " & " for processing 
-	Remove all special characters except space and dot
-	Change " AND " back to " & " 
-	Replace multiple spaces with a single space, also trim spaces on both side
-	Remove '.' at the end
-	Remove '.' at the beginning
There were 8541 unique values after performing these cleansing operations.
##### 2.8.2	LTD append problem
There were also many duplicate company names with and without LTD appended to the end. In order to fix this the following operations were performed:
-	Extract all the company names with LTD at the end (list1)
-	Create a duplicate list with LTD removed from the end (list2)
-	Search the Company values for a match in list2
-	If found, replace with corresponding value from list1 (with LTD appended)
There were 7930 unique values after performing the LTD cleansing operations.
##### 2.8.3	Shortened name problem
There were many Company names that are a shortened version of a longer company name for example 'SPECIALIST RECRUIT': 'SPECIALIST RECRUITMENT PARTNERS LTD'.  To fix this the following steps were performed:
-	Sort all the company names
-	Check if company name is a substring of the next company name
-	If yes, create a dictionary of {substring : longerstring} which can be used to further refine the Company duplicates
-	Check list of appropriate replacements
-	Remove any dictionary values that should not be replaced (due to ambiguity or incorrect mapping)
-	Update the company names
A dictionary of company names resulted from this process.  A visual inspection resulted in the decision to remove some company names from this dictionary due to ambiguity or incorrect mapping. 
The substring : longerstring dictionary was applied three times in order to map company names that had several different versions for example:
 'BMS': 'BMS ENGINEERING',
 'BMS ENGINEERING': 'BMS ENGINEERING RECRUITMENT',
 'BMS ENGINEERING RECRUITMENT': 'BMS ENGINEERING RECRUITMENT LLP',
There were 7454 unique company names after the substring cleaning was applied three times.
##### 2.8.4	Close matches
In order to check for close matches due to misspellings or missing '&' I used a function which checked the similarity between two strings using SequenceMatcher and created a dictionary of low frequency company names : high frequency company names.  Setting min threshold to 0.95 and max threshold to 1 the resulted in a dictionary of replacement values.  After visual inspection, some of these values were chosen to not be replaced.
Note this is quite a slow process to run so go grab a coffee after you run it!!  A more efficient matching process such as the FuzzyWuzzy package should be investigated if more matching is required.
##### 2.8.5	Matching by inspection
By inspection a few matches were identified and also manually updated.
##### 2.8.6	Summary
From an initial 9065 unique company names, the above data cleansing has reduced this down to 7418 unique company names.  There are still more that could be done with this field but there are diminishing returns, the majority of the errors have been found and corrected above.

#### 2.9	OpenDate and CloseDate
##### 2.9.1	OpenDate
A regular expression was used to check for OpenDate values with an invalid date format.  Date format should be yyyymmddThhmmss. For example: 20120104T150000 means 3pm, 4th January 2012.
There was one record identified: Id = 72245549, OpenDate = 20122302T120000.  It was assumed that the month and day were mixed up in this record so it was updated to 20120223T120000.
##### 2.9.2	CloseDate
The same process was applied to CloseDate and there were no incorrectly formatted dates in this field.
##### 2.9.3	OpenDate CloseDate time checking
In order to check if there were any invalid entries having CloseDate earlier than OpenDate the following steps were applied:
-	Extract the first yyyyddmm from the OpenDate string and add to new column called OpenDatex
-	Convert OpenDatex to datetime using to_datetime
-	Extract the first yyyyddmm from the CloseDate string and add to new columns called CloseDatex
-	Convert CloseDatex to datetime using to_datetime
-	Check if CloseDatex is earlier than OpenDatex
-	Fix incorrect entries
-	Drop unnecessary columns
After applying this process, there were 5 entries with incorrect values and all of these entries were within a maximum of 3 months of each other so it was assumed that they got mixed up and the values were swapped.

#### 2.10	Salary
Salary was composed of many different formatted values.  Listed below is an outline of the different salary types and the cleansing method performed.  Values that required further processing were separated out from the main dataset prior to processing then re-joined into the data1 dataframe with correctly formatted values.

- 1585 NaN values: Leave as NaN
- 773	‘0’ values: Convert to NaN
- 373 values '-': Convert to NaN
- 50	[2digits]K e.g. 50K: Extract digits convert to float and multiply by 1000
- 100	[5digits]/YEAR:	Extract digits convert to float
- 71	[5digits] per annum e.g. 50000 PER ANNUM:	Extract digits convert to float
- 29	[5digits] pa e.g. 50000 PA:	Extract digits convert to float
- 333	[4digits] e.g. 5000:	Convert to float
- 2	[4digits.digits] e.g. 5000.0:	Convert to float
- 51002	[5digits] e.g. 50000:	Convert to float
- 640	[5digits.digits] e.g. 50000.0:	Convert to float
- 4	[6digits] e.g. 100000:	Convert to float
- 2	[8digits] e.g. 10000000:	Convert to NaN
- 2	[decimal] p/h e.g. 11.03 P/H:	Extract digits convert to float and multiply by 1040 
- 3	[2digits.digits] per hour e.g. 17.7 PER HOUR:	Extract digits convert to float and multiply by 1040a
- 100	[5digits] - [5digits] e.g. 34000 - 36000:	Extract digits find average convert to float
- 100	[5digits] to [5digits] e.g. 34000 TO 36000:	Extract digits find average convert to float






