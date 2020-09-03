---
layout: post
title: "openpyxl 병합된 셀 분리하고 내용 그대로 채우기"
tags: Python
comments: true
---

```python
from openpyxl import load_workbook
from openpyxl.utils.cell import range_boundaries
from copy import copy

# 병합된 셀을 분리하여 기존 데이터로 채우고 정렬, 포맷도 그대로 적용한다.
def split_merged_cells():
    wb = load_workbook(filename = 'test.xlsx')

    for sheet_name in wb.sheetnames:
        sheet = wb[sheet_name]
        mcr_coord_list = [mcr.coord for mcr in sheet.merged_cells.ranges]

        for mcr in mcr_coord_list:
            min_col, min_row, max_col, max_row = range_boundaries(mcr)
            top_left_cell_value = sheet.cell(row=min_row, column=min_col).value
            top_left_cell_alignment = sheet.cell(row=min_row, column=min_col).alignment
            top_left_cell_format = sheet.cell(row=min_row, column=min_col).number_format
            sheet.unmerge_cells(mcr)
            for row in sheet.iter_rows(min_col=min_col, min_row=min_row, max_col=max_col, max_row=max_row):
                for cell in row:
                    cell.value = top_left_cell_value
                    cell.alignment = copy(top_left_cell_alignment)
                    cell.number_format = copy(top_left_cell_format)

    wb.save('merged_tmp.xlsx')
```

모든 시트를 반복하며 병합된 셀의 범위를 읽어와 리스트 형태로 mcr_coord_list에 넣습니다.

```python
for sheet_name in wb.sheetnames:
        sheet = wb[sheet_name]
        mcr_coord_list = [mcr.coord for mcr in sheet.merged_cells.ranges]
```

병합되있는 셀의 값, 정렬, 포맷 방식을 저장해놓습니다.

```python
top_left_cell_value = sheet.cell(row=min_row, column=min_col).value
top_left_cell_alignment = sheet.cell(row=min_row, column=min_col).alignment
top_left_cell_format = sheet.cell(row=min_row, column=min_col).number_format
```

리스트에서 읽어온 범위에 맞춰 하나씩 병합을 해제합니다.

```python
sheet.unmerge_cells(mcr)
```

병합을 해제하고 남은 비어있는 셀에 값을 그대로 채워줍니다.

```python
for row in sheet.iter_rows(min_col=min_col, min_row=min_row, max_col=max_col, max_row=max_row):
    for cell in row:
        cell.value = top_left_cell_value
        cell.alignment = copy(top_left_cell_alignment)
        cell.number_format = copy(top_left_cell_format)
```