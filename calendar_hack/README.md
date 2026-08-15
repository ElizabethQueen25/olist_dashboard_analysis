# CALENDAR HACK! 📅

Hi there! Are you struggling to create each column and category for your calendar table? 👀 Then you might find this tool really helpfull. 

**Important:** Don't forget to replace the name of the correct calendar column into this query

```
let
    Source = #"olist_orders_dataset"[order_purchase_timestamp],
    FechaInicio = #date(Date.Year(List.Min(Source)),1,1),
    FechaFinal = #date(Date.Year(List.Max(Source)),12,31),
    ListaInicioFinal = {Number.From(FechaInicio)..Number.From(FechaFinal)},
    #"Converted to Table" = Table.FromList(ListaInicioFinal, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Changed Type" = Table.TransformColumnTypes(#"Converted to Table", {{"Column1", type date}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type",={{"Column1", "Fecha"}}),
    #"Added Custom Column" = Table.AddColumn(#"Renamed Columns", "Custom", each Text.Combine({Date.ToText([Fecha], "yyyy"), Date.ToText([Fecha], "MM"), Date.ToText([Fecha], "dd")})),
    #"Inserted Year" = Table.AddColumn(#"Added Custom Column", "Year", each Date.Year([Fecha]), Int64.Type),
    #"Inserted Quarter" = Table.AddColumn(#"Inserted Year", "Quarter", each Date.QuarterOfYear([Fecha]), Int64.Type),
    #"Inserted Month" = Table.AddColumn(#"Inserted Quarter", "Month", each Date.Month([Fecha]), Int64.Type),
    #"Inserted Day" = Table.AddColumn(#"Inserted Month", "Day", each Date.Day([Fecha]), Int64.Type),
    #"Inserted Merged Column" = Table.AddColumn(#"Inserted Day", "Merged", each Text.Combine({"T", Text.From([Quarter], "es-MX")}), type text),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted Merged Column", "Month Name", each Date.MonthName([Fecha]), type text),
    #"Inserted First Characters" = Table.AddColumn(#"Inserted Month Name", "First Characters", each Text.Start([Month Name], 3), type text),
    #"Inserted Day of Week" = Table.AddColumn(#"Inserted First Characters", "Day of Week", each Date.DayOfWeek([Fecha]), Int64.Type),
    #"Inserted Week of Year" = Table.AddColumn(#"Inserted Day of Week", "Week of Year", each Date.WeekOfYear([Fecha]), Int64.Type),
    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Week of Year", "Week of Month", each Date.WeekOfMonth([Fecha]), Int64.Type),
    #"Inserted End of Week" = Table.AddColumn(#"Inserted Week of Month", "End of Week", each Date.EndOfWeek([Fecha], Day.Monday), type date),
    #"Inserted Day of Year" = Table.AddColumn(#"Inserted End of Week", "Day of Year", each Date.DayOfYear([Fecha]), Int64.Type),
    #"Inserted Day Name" = Table.AddColumn(#"Inserted Day of Year", "Day Name", each Date.DayOfWeekName([Fecha]), type text),
    #"Inserted First Characters1" = Table.AddColumn(#"Inserted Day Name", "First Characters.1", each Text.Start([Day Name], 3), type text),
    #"Inserted Merged Column1" = Table.AddColumn(#"Inserted First Characters1", "Merged.1", each Text.Combine({Text.From([Year], "es-MX"), [Month Name]}, "-"), type text),
    #"Inserted First Characters2" = Table.AddColumn(#"Inserted Merged Column1", "First Characters.2", each Text.Start([Custom], 6), type text),
    #"Split Column by Position" = Table.SplitColumn(#"Inserted First Characters2", "First Characters.2", Splitter.SplitTextByPositions({0, 4}, false), {"First Characters.2.1", "First Characters.2.2"}),
    #"Merged Columns" = Table.CombineColumns(#"Split Column by Position", {"First Characters.2.1", "First Characters.2.2"}, Combiner.CombineTextByDelimiter("-", QuoteStyle.None), "Merged.2"),
    #"Inserted Merged Column2" = Table.AddColumn(#"Merged Columns", "Merged.2", each Text.Combine({Text.From([Year], "es-MX"), "/", [First Characters.2.2]}), type text),
    #"Replaced Value" = Table.ReplaceValue(#"Inserted Merged Column2",0,7,Replacer.ReplaceValue,{"Day of Week"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Replaced Value",={{"Custom", "FechaSK"}, {"Year", "Año"}, {"Quarter", "Trimestre"}, {"Month", "Mes"}}),
    #"Inserted Start of Month" = Table.AddColumn(#"Renamed Columns1", "Start of Month", each Date.StartOfMonth([Fecha]), type date),
    #"Renamed Columns2" = Table.RenameColumns(#"Inserted Start of Month",={{"Start of Month", "InicioMes"}}),
    #"Inserted End of Month" = Table.AddColumn(#"Renamed Columns2", "End of Month", each Date.EndOfMonth([Fecha]), type date),
    #"Renamed Columns3" = Table.RenameColumns(#"Inserted End of Month",={{"End of Month", "FinMes"}})
in
    #"Renamed Columns3"

```
