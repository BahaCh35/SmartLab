# Analytics Page - Export Functionality Guide

## Overview
The Engineer's Analytics Page now supports full export functionality for **PDF**, **CSV**, and **Excel** formats with real data from your lab management system.

---

## Export Features Implemented

### 1. **CSV Export** ✅
- **Format**: Comma-separated values
- **Includes**:
  - Checkout statistics (total, active, returned, overdue)
  - Equipment utilization table
  - Top users ranking table
- **Filename**: `analytics-YYYY-MM-DD.csv`
- **Compatible with**: Excel, Google Sheets, any spreadsheet app

### 2. **Excel Export** ✅
- **Format**: Multi-sheet workbook (.xlsx)
- **Sheets Included**:
  1. **Overview** - Checkout statistics summary
  2. **Equipment Utilization** - Equipment usage data
  3. **Top Users** - User rankings with return rates
  4. **Categories** - Equipment categories breakdown
- **Filename**: `analytics-YYYY-MM-DD.xlsx`
- **Compatible with**: Microsoft Excel, Google Sheets, LibreOffice

### 3. **PDF Export** ✅
- **Format**: Professional PDF document
- **Pages**:
  1. **Page 1**:
     - Report title and generation timestamp
     - Checkout statistics
     - Equipment utilization table (formatted)
  2. **Page 2**:
     - Top users table with rankings
- **Styling**: Professional header styling (blue headers, proper formatting)
- **Filename**: `analytics-YYYY-MM-DD.pdf`
- **Compatible with**: All PDF readers

---

## How to Use

### Step 1: Select Export Format
Click the dropdown in the top-right corner and choose:
- **CSV** - Simple spreadsheet format
- **Excel** - Multi-sheet workbook
- **PDF** - Professional document

### Step 2: Click Export Button
The button shows "Exporting..." while the file is being generated.

### Step 3: Download
Your browser will automatically download the file with the current date appended.

---

## Data Included in Each Export

### All Formats Include:
- **Total Checkouts**: Sum of all equipment checkouts
- **Active Checkouts**: Currently checked-out items
- **Returned Checkouts**: Successfully returned items
- **Overdue Checkouts**: Past due items

### Equipment Utilization (All Formats):
- Equipment name
- Utilization percentage (0-100%)
- Total checkout count

### Top Users (All Formats):
- User ranking (#1, #2, etc.)
- User name
- Total checkouts
- Return rate percentage

### Additional Details (Excel & PDF):
- Category breakdown of equipment types
- Generation timestamp
- Multi-page formatting (PDF)

---

## Technical Implementation

### Libraries Used:
- **jsPDF** - PDF generation with professional formatting
- **jsPDF-AutoTable** - Beautiful tables in PDF documents
- **XLSX (SheetJS)** - Excel workbook creation
- **Native Blob API** - CSV download via browser

### Data Sources:
- `checkoutService.getCheckouts()` - All checkout records
- `checkoutService.getEquipment()` - Equipment catalog
- `equipmentReservationService.getReservations()` - Reservation data
- `checkoutService.getCheckoutStats()` - Pre-calculated statistics

### Export Logic:
Each format uses optimized code paths:
```
CSV → Text data → Blob → Download
Excel → Workbook → Multiple sheets → Binary file → Download
PDF → Document structure → Tables → Multi-page → Download
```

---

## File Naming Convention

All exported files follow this pattern:
```
analytics-YYYY-MM-DD.{extension}
```

**Examples**:
- `analytics-2026-03-05.csv`
- `analytics-2026-03-05.xlsx`
- `analytics-2026-03-05.pdf`

---

## Browser Compatibility

| Feature | Chrome | Firefox | Safari | Edge | IE |
|---------|--------|---------|--------|------|-----|
| CSV     | ✅ | ✅ | ✅ | ✅ | ⚠️ |
| Excel   | ✅ | ✅ | ✅ | ✅ | ⚠️ |
| PDF     | ✅ | ✅ | ✅ | ✅ | ⚠️ |

IE = Not recommended (use modern browsers)

---

## Features

### Real-Time Data
- All exports pull live data from the mock services
- Click "Refresh" button to reload data before exporting
- Automatically includes latest checkout and user information

### Error Handling
- If export fails, user receives an error alert
- Export button is disabled while data is loading
- Proper error logging to browser console

### User Experience
- Export button shows "Exporting..." state while processing
- Automatic file download with proper naming
- Success confirmation message

---

## Future Enhancements

Potential improvements for future versions:
1. **Scheduled Reports** - Automatically email reports
2. **Custom Date Range** - Filter by specific dates
3. **Chart Embedment** - Include visualizations in PDF
4. **Email Integration** - Send reports directly
5. **Cloud Storage** - Save to Google Drive, OneDrive
6. **Report Templates** - Customizable report formats

---

## Testing the Export

1. Navigate to Engineer Dashboard → Analytics
2. View the data loads with real checkout information
3. Click "Refresh" to ensure latest data
4. Select "CSV" from dropdown → Click "Export"
5. Verify file downloads as `analytics-YYYY-MM-DD.csv`
6. Repeat with "Excel" and "PDF" formats
7. Wait for success message after each export

---

**Status**: ✅ Production Ready
**Version**: 1.0.0
**Last Updated**: 2026-03-05
