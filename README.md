# Blog 0 - January 31, 2020
## Using Python 3.4 + to Work with Excel Spreadsheets

Excel is a popular and powerful spreadsheet application for Windows 10 (and earlier versions). The openpyxl module allows your Python programs to read and modify Excel spreadsheet files. If you have the boring task of copying certain data from one spreadsheet and pasting it into another one, you can use Python to do this for you. 

**Note: If you do not have Excel, there are alternatives to open .xlsx files that work on Windows, OS X, and Linux - LibreOffice Calc and OpenOffice Calc!**

- Excel Documents

First, let’s go over some basic definitions: An Excel spreadsheet document is called a workbook. A single workbook is saved in a file with the .xlsx extension. Each workbook can contain multiple worksheets. The sheet the user is currently viewing is called the active sheet. Each sheet has columns (addressed by letters starting at A) and rows (addressed by numbers starting at 1). A box at a particular column and row is called a cell. Each cell can contain a number or text value. The grid of cells with data makes up a sheet.

- Installing the openpyxl Module

Open the Python idle, and run: import openpyxl

If the module was correctly installed, there should be no error messages. Remember to import the openpyxl module before running the interactive shell examples in this chapter, or you’ll get " NameError: name 'openpyxl' is not defined error. "

- Opening Excel Documents with OpenPyXL

Once you’ve imported the openpyxl module, you’ll be able to use the openpyxl.load_workbook() function. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. type(wb)
> <class 'openpyxl.workbook.workbook.Workbook'>


The openpyxl.load_workbook() function takes in the filename and returns a value of the workbook data type. This Workbook object represents the Excel file, a bit like how a File object represents an opened text file.

- Getting Sheets from the Workbook

You can get a list of all the worksheet names in the workbook by calling the get_sheet_names() method. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. wb.get_sheet_names()
> 'Sheet1', 'Sheet2', 'Sheet3'
5. sheet = wb.get_sheet_by_name('Sheet3')
6. sheet 
> <Worksheet "Sheet2">
7. type(sheet) 
> <class 'openpyxl.worksheet.worksheet.Worksheet'>
8. sheet.title 
> 'Sheet2'

Each sheet is represented by a Worksheet object, which you can find by passing the sheet name string to the get_sheet_by_name() workbook method. Also, you can read the active member variable of a Workbook object to get the workbook’s active sheet. Once you have the Worksheet object, you can get its name from the title attribute.

- Getting Cells from the Sheets

Once you have a Worksheet object, you can access a Cell object by its name. Enter the following into the interactive shell:
1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. sheet = wb.get_sheet_by_name('Sheet1')
4. sheet['A1'] 
> <Cell Sheet1.A1>
5. sheet['A1'].value datetime.datetime(2015, 4, 5, 13, 34, 2)
6. c = sheet['B1']
7. c.value 
> 'Apples'
8. 'Row ' + str(c.row) + ', Column ' + c.column + ' is ' + c.value
> 'Row 1, Column B is Apples'
9. 'Cell ' + c.coordinate + ' is ' + c.value
> 'Cell B1 is Apples'
10. sheet['C1'].value
> 73

The Cell object has a value attribute that contains the value stored in that cell. Cell objects also have row, column, and coordinate attributes that provide location information for the cell.

Here, accessing the value attribute of our Cell object for cell B1 gives us the string 'Apples'. The row attribute gives us the integer 1, the column attribute gives us 'B', and the coordinate attribute gives us 'B1'.

- Converting Between Column Letters and Numbers

To convert from letters to numbers, call the openpyxl.cell.column_index_from_string() function. To convert from numbers to letters, call the openpyxl.cell.get_column_letter() function. Enter the following into the interactive shell:

1. import openpyxl
2. from openpyxl.cell import get_column_letter, column_index_from_string
3. get_column_letter(1)
> 'A'
4. get_column_letter(2)
> 'B'
5. get_column_letter(27)
> 'AA'
6. get_column_letter(900)
> 'AHP'
7. wb = openpyxl.load_workbook('example.xlsx')
8. sheet = wb.get_sheet_by_name('Sheet1')
9. get_column_letter(sheet.max_column)
> 'C'
10. column_index_from_string('A')
> 1
11. column_index_from_string('AA')
> 27

The function column_index_string() does the reverse: You pass it the letter name of a column, and it tells you what number that column is. You don’t need to have a workbook loaded to use these functions.

- Getting Rows and Columns from the Sheets

You can slice Worksheet objects to get all the Cell objects in a row, column, or rectangular area of the spreadsheet. Then you can loop over all the cells in the slice. Enter the following into the interactive shell:

1. import openpyxl
2. wb = openpyxl.load_workbook('example.xlsx')
3. sheet = wb.get_sheet_by_name('Sheet1')
4. tuple(sheet['A1':'C3'])
> ((<Cell Sheet1.A1>, <Cell Sheet1.B1>, <Cell Sheet1.C1>), (<Cell Sheet1.A2>,
> <Cell Sheet1.B2>, <Cell Sheet1.C2>), (<Cell Sheet1.A3>, <Cell Sheet1.B3>,
> <Cell Sheet1.C3>))
5. for rowOfCellObjects in sheet['A1':'C3']:
6. for cellObj in rowOfCellObjects:
7. print(cellObj.coordinate, cellObj.value)
8. print('--- END OF ROW ---')

Here is the result:

> A1 2015-04-05 13:34:02
> B1 Apples
> C1 73
> --- END OF ROW ---
> A2 2015-04-05 03:41:23
> B2 Cherries
> C2 85
> --- END OF ROW ---
> A3 2015-04-06 12:46:51
> B3 Pears
> C3 14
> --- END OF ROW ---
   
First, we specify that we want the Cell objects in the rectangular area from A1 to C3, and we get a Generator object containing the Cell objects in that area. To help us visualize this Generator object, we can use tuple() on it to display its Cell objects in a tuple.

This tuple contains three tuples: one for each row, from the top of the desired area to the bottom. Each of these three inner tuples contains the Cell objects in one row of our desired area, from the leftmost cell to the right. So, our slice of the sheet contains all the Cell objects in the area from A1 to C3, starting from the top-left cell to the bottom-right cell.

Next, to print the values of each cell in the area, we use two for loops. In line 5, the outer for loop goes over each row in the slice. Then,in line 6, the nested for loop goes through each cell for each row.
