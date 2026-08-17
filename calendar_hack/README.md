# Dynamic Calendar Table for Power BI / Power Query 📅 📅

A quick way to generate a complete, dynamic Date Dimension table for your Power BI reports

**Important:** Update the "Source" step with your actual fact table and date column name: #"YourTableName"[YourDateColumn]

```
let
    Source = #"olist_orders_dataset"[order_purchase_timestamp],  /// --> your fact table and date column goes here: "YourTableName"[YourDateColumn]
    StartDate = #date(Date.Year(List.Min(Source)), 1, 1),
    EndDate = #date(Date.Year(List.Max(Source)), 12, 31),
    DateList = {Number.From(StartDate)..Number.From(EndDate)},
    #"Converted to Table" = Table.FromList(DateList, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Changed Type" = Table.TransformColumnTypes(#"Converted to Table", {{"Column1", type date}}),
    #"Renamed Date Column" = Table.RenameColumns(#"Changed Type", {{"Column1", "#Date"}}),
    #"Added DateKey" = Table.AddColumn(#"Renamed Date Column", "#DateKey", each Text.Combine({Date.ToText([#"#Date"], "yyyy"), Date.ToText([#"#Date"], "MM"), Date.ToText([#"#Date"], "dd")}), type text),
    #"Inserted Year" = Table.AddColumn(#"Added DateKey", "#Year", each Date.Year([#"#Date"]), Int64.Type),
    #"Inserted Quarter" = Table.AddColumn(#"Inserted Year", "#Quarter", each Date.QuarterOfYear([#"#Date"]), Int64.Type),
    #"Inserted Month" = Table.AddColumn(#"Inserted Quarter", "#Month", each Date.Month([#"#Date"]), Int64.Type),
    #"Inserted Day" = Table.AddColumn(#"Inserted Month", "#Day", each Date.Day([#"#Date"]), Int64.Type),
    #"Inserted Quarter Text" = Table.AddColumn(#"Inserted Day", "Quarter", each Text.Combine({"Q", Text.From([#"#Quarter"], "en-US")}), type text),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted Quarter Text", "Month", each Date.MonthName([#"#Date"], "en-US"), type text),
    #"Inserted Short Month" = Table.AddColumn(#"Inserted Month Name", "Short Month", each Text.Start([Month], 3), type text),
    #"Inserted Day of Week" = Table.AddColumn(#"Inserted Short Month", "#Day of Week", each if Date.DayOfWeek([#"#Date"], Day.Monday) = 0 then 7 else Date.DayOfWeek([#"#Date"], Day.Monday), Int64.Type),
    #"Inserted Week of Year" = Table.AddColumn(#"Inserted Day of Week", "#Week of Year", each Date.WeekOfYear([#"#Date"]), Int64.Type),
    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Week of Year", "#Week of Month", each Date.WeekOfMonth([#"#Date"]), Int64.Type),
    #"Inserted End of Week" = Table.AddColumn(#"Inserted Week of Month", "#End of Week", each Date.EndOfWeek([#"#Date"], Day.Monday), type date),
    #"Inserted Day of Year" = Table.AddColumn(#"Inserted End of Week", "#Day of Year", each Date.DayOfYear([#"#Date"]), Int64.Type),
    #"Inserted Day Name" = Table.AddColumn(#"Inserted Day of Year", "Day Name", each Date.DayOfWeekName([#"#Date"], "en-US"), type text),
    #"Inserted Short Day" = Table.AddColumn(#"Inserted Day Name", "Short Day", each Text.Start([Day Name], 3), type text),
    #"Inserted Year-Month" = Table.AddColumn(#"Inserted Short Day", "Year-Month", each Text.Combine({Text.From([#"#Year"], "en-US"), [Month]}, "-"), type text),
    #"Inserted Year/Month Code" = Table.AddColumn(#"Inserted Year-Month", "Year/Month Code", each Text.Combine({Text.From([#"#Year"], "en-US"), "/", Date.ToText([#"#Date"], "MM")}), type text),
    #"Inserted Start of Month" = Table.AddColumn(#"Inserted Year/Month Code", "#Start of Month", each Date.StartOfMonth([#"#Date"]), type date),
    #"Inserted End of Month" = Table.AddColumn(#"Inserted Start of Month", "#End of Month", each Date.EndOfMonth([#"#Date"]), type date)
in
    #"Inserted End of Month"

```
