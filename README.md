# Movie Dashboard
## Table of Content
[Problem Statement](#problem-statement)
[Data Source](#data-source)
[Tools](#tools)
[]
[]
### Problem Statement
Netflix wants to better understand which movie they should produce next, including the most suitable actors and directors. We have a dataset containing movie budgets, box office performance, actors, directors, and genres. Your task is to build an Excel dashboard that provides insights into this dataset. The dashboard should help identify:

- The best-performing actors
- The top movies based on box office metrics
- Director performance
- Genre trends
- Seasonal patterns in movie performance
- Any additional insights that can guide future production decisions
  
The final dashboard should be clear, interactive, and visually compelling, enabling Netflix to make data-driven decisions.
### Data Source
Movie Data : The primary dataset used for this analysis is the "Movie Data Homework.xlsx" file, containing detailed information about each movie's performance (box office and budget), actors, directors and genres. 
You can download the original datasource here: [Movie Dataset Excel file](https://github.com/user-attachments/files/24380258/Movies_Data_Homework.xlsx)
### Tools
1. Power Query - I used Power Query for Data Cleaning
2. Excel - I used Excel for Data Analysis
3. Pivot Tables - for Creating the dashboard and Visualizations

### Data Cleaning and Preparation
- Data loading and inspection.
- Handling errors, missing values.
- Data cleaning and formatting. The excel file after the data cleaning & preparation process can be downloaded here - [Movies Dashboard](https://github.com/user-attachments/files/24380342/Movie.data.Igor.xlsx)
### Dashboard
<img width="810" height="614" alt="image" src="https://github.com/user-attachments/assets/99771be6-1e62-456e-b52f-6a0c695a3d31" />

### M Code

```
let
  Source = Excel.Workbook(File.Contents("C:\Users\Workstation\Downloads\Movies_Data_Homework.xlsx"), null, true),
  Navigation = Source{[Item = "Movie Data", Kind = "Sheet"]}[Data],
  #"Promoted headers" = Table.PromoteHeaders(Navigation, [PromoteAllScalars = true]),
    #"Merged queries" = Table.NestedJoin(#"Promoted headers", {"Genre_First_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
    #"Expanded Genres" = Table.ExpandTableColumn(#"Merged queries", "Genres", {"Genre"}, {"Genre"}),
  #"Removed columns" = Table.RemoveColumns(#"Expanded Genres", {"Column14", "Column15", "Column16", "Column17", "Column18", "Column19", "Column20", "Column21"}),
  #"Reordered columns" = Table.ReorderColumns(#"Removed columns", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Merged queries 1" = Table.NestedJoin(#"Reordered columns", {"Genre_Second_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
  #"Expanded Genres 1" = Table.ExpandTableColumn(#"Merged queries 1", "Genres", {"Genre"}, {"Genre.1"}),
  #"Renamed columns" = Table.RenameColumns(#"Expanded Genres 1", {{"Genre.1", "Genre_Second"}}),
  #"Reordered columns 1" = Table.ReorderColumns(#"Renamed columns", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Merged queries 2" = Table.NestedJoin(#"Reordered columns 1", {"Director_First_ID"}, Directors, {"ID"}, "Directors", JoinKind.LeftOuter),
  #"Expanded Directors" = Table.ExpandTableColumn(#"Merged queries 2", "Directors", {"Director"}, {"Director"}),
  #"Reordered columns 2" = Table.ReorderColumns(#"Expanded Directors", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Merged queries 3" = Table.NestedJoin(#"Reordered columns 2", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors" = Table.ExpandTableColumn(#"Merged queries 3", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 3" = Table.ReorderColumns(#"Expanded Actors", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Merged queries 4" = Table.NestedJoin(#"Reordered columns 3", {"Cast_Second_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 1" = Table.ExpandTableColumn(#"Merged queries 4", "Actors", {"Actor"}, {"Actor.1"}),
  #"Renamed columns 1" = Table.RenameColumns(#"Expanded Actors 1", {{"Actor.1", "Actor_2"}}),
  #"Reordered columns 4" = Table.ReorderColumns(#"Renamed columns 1", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Actor_2", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Reordered columns 4",{{"Release Date", type date}, {"Genre_Second_ID", Int64.Type}, {"Cast_Fourth_ID", Int64.Type}}),
    #"Added Custom" = Table.AddColumn(#"Changed Type", "ROI", each ([#"Box Office Revenue ($)"]-[#"Budget ($)"])/[#"Budget ($)"]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"ROI", Percentage.Type}})
in
    #"Changed Type1"
```

### Recommendations

Top 5 genres are Action, Comedy, Drama, Sci-Fi, Adventure. I would recommend Netflix to produce a movie with one of these genres as they brought in more in box office revenie based on the data from 2012 to 2016
<img width="506" height="174" alt="image" src="https://github.com/user-attachments/assets/b7162fe8-ce51-44f0-8219-e1cc7a11ee6e" />
