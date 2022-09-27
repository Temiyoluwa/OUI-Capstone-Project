# Project Name: OUI-Capstone-Project

----
# Project Objective:
The Palmoria Group, a manufacturing company based in the Nigeria is embroiled in issues bordering on gender inequality in its 3 regions which doesn’t look good for the owners of the business based on their ambition to scale the business to other regions and even overseas. The objective therefore is to identify key areas within the business that could spring up issues concerning gender inequality so as to address them immediately.

----
# Data Sourcing: 
The data used for the analysis was provided by the mentors of the OUI BootCamp during the 30 days learning program

----
# Data Transformation: 
The data transformation process included changing the data type present, replacing some data value,filtering rows, removing duplicates all present in the code below:
let
    Source = Csv.Document(File.Contents("C:\Users\user\Desktop\DA\emp-data.csv"),[Delimiter=",", Columns=6, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Name", type text}, {"Gender", type text}, {"Department", type text}, {"Salary", Currency.Type}, {"Location", type text}, {"Rating", type text}}),
    #"Replaced Value" = Table.ReplaceValue(#"Changed Type","","N/A",Replacer.ReplaceValue,{"Gender"}),
    #"Filtered Rows" = Table.SelectRows(#"Replaced Value", each ([Department] <> "NULL") and ([Salary] <> null) and ([Rating] <> "Not Rated")),
    #"Removed Duplicates" = Table.Distinct(#"Filtered Rows", {"Name", "Department", "Location", "Rating"}),
    #"Merged Queries" = Table.NestedJoin(#"Removed Duplicates", {"Department", "Rating"}, #"bonus mapping", {"Department", "Attribute"}, "bonus mapping", JoinKind.LeftOuter),
    #"Expanded bonus mapping" = Table.ExpandTableColumn(#"Merged Queries", "bonus mapping", {"Value"}, {"Value"}),
    #"Grouped Rows" = Table.Group(#"Expanded bonus mapping", {"Department"}, {{"Count", each _, type table [Name=nullable text, Gender=nullable text, Department=nullable text, Salary=nullable number, Location=nullable text, Rating=nullable text, Value=nullable number]}}),
    #"Expanded Count" = Table.ExpandTableColumn(#"Grouped Rows", "Count", {"Name", "Gender", "Salary", "Location", "Rating", "Value"}, {"Name", "Gender", "Salary", "Location", "Rating", "Value"}),
    #"Renamed Columns" = Table.RenameColumns(#"Expanded Count",{{"Location", "Region"}})
in
    #"Renamed Columns"
    I also applied various DAX functions to obtain needed figures.
    
    ----
    
    #Findings and Recomendations:
The analysis showed that as a group looking to expand its region nationally and internationally, Palmoria Manufacturing Group has some balanced gender distribution in it's organization, although the Male gender ranks highest in most department and salary payment. The need to ensure a more balanced gender rank in terms of department and salary payment is recomended. Palmoria may also seek to employ more female staffs with some, particular focus on the Legal department in Abuja region. Also the company should ensure every N/A employee reveal their gender so as to have a more appropriate data on the gender position in the organization, thus ensuring there's a more precise action to be taken.
