compareRESP: BASH scripts that compare RESP files and finds relevant differences between the two in a summary. 
Created with the differences between NCEDC and SIS RESP files in mind. 
Last updated 12/27/2023. 

Usage: compareRESP -n network_code -s station -l location_of_Non-SIS_RESP_Files 
-t test_or_production -f preview_or_publication -p percent_ for_range

If information is missing, a prompt will appear asking for that information. 
If there is a dataless file for the network & station present in the location where 
compareRESP is being run, test/production and preview/production do not need to be
provided and won't be asked for.

File Overview: 
compareRESP: collects information and calls other files; diff performed 
normalizeDetails: very ad hoc; a series of sed commands to make the RESP files more similar
mergeEpochs: combines epochs if contents are the same; for the sake of consistency 
makeRESPSummary: collect important differences; check if differences in poles, zeros, 
polynomials, gains, sensitivities are within the provided percent of each other and ignore 
if they are.

Dependencies:
rdseed should be in your environment. Available for download through 
IRIS: https://ds.iris.edu/ds/nodes/dmc/software/downloads/rdseed/

Notes & Details:
If a dataless file is not present for the station, an attempt will be made to download 
the dataless from SIS. 
Click "Publish Files or Access Published Files" on the far right of the station page, 
choose publish or preview, and then wait for SIS email confirmation before running 
compareRESP.

A location for local non-SIS RESP files will need to be provided. If the first character 
isn't a /, the location is assumed to be relative to the current location. 

A folder with the name format networkCode_station_RESP_Comparison will be removed and 
created when compareRESP is run. 
Multiple sub-folders will be created in the location where compareRESP is run including a 
copy of the local RESP files (DC_RESP_station), a folder for the RESP files from the 
dataless (SIS_RESP_station), and folders for the altered/normalized RESP files 
(DC_RESP_station_Altered & SIS_RESP_station_Altered). After normalizeDetails and 
mergeEpochs, a diff is performed between the SIS and non-SIS RESP files and a folder called
Altered_Diffs will have .diff files with the differences between the RESP files. 
makeRESPSummary will then look for important differences including extra epochs that may be
 present in one RESP file and not the other (these are usually do to the files not merging 
properly as the result of some information being different epoch to epoch) and 
inconsistencies in poles, zeros, gains, polynomials, and sensitivities (will be referred to as 
numeric inconsistencies for the sake of brevity). If these numeric inconsistencies are greater 
than the provided percent rate, they are included in an end summary. If no percent value is 
provided, 3% is used as default. 

mergeEpochs and makeRESPSummary make the somewhat reasonable assumption that all RESP file 
headers have similar structure compared to the SIS RESP files. 
normalizeDetails is not general at all and most of the swaps made are ones encountered in 
practice/based off of information not relevant for comparison purposes 
(taking out FIR filter coefficients for example). By no means is the list of changes 
comprehensive. Changes will likely need to be added to dictionary as a superficial way to 
get rid 
of inconsistencies. Format: ["how now"]="brown cow" replaces all instances of "how now" 
with "brown cow". All text in the RESP files is made lowercase for slightly more generality such 
that anything in dictionary should also be lowercase. A \ should be added before certain 
characters such as / and *.  

Things that I would like to work on next, but likely won't happen for a while:
1. Having the output printed only once per instance with a header that indicates what files the 
output applies to.
2. More slack when it comes to merging epochs within a file. If 2 epochs don't merge due to 
poles/zeros/polynomials/gains/sensitivities that are all within the percent range provided of each 
other, merge anyway.
