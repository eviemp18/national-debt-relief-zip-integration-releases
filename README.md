# national-debt-relief-zip-integration-releases
This repository hosts public Windows installer releases for the NDR Zip Integration application. The application checks vendors in `vendor_budget.xlsx` or a similar uploaded workbook against agreement data from the Zip API. It writes verification results back into the workbook and creates a dated archive copy after each successful run.

## Requirements for end-user installation

The installer is self-contained. End users do not need Python, pip, Git, or this repository on their machines.

## Installer behavior

During install, the user is prompted to provide:

- `ZIP_API_KEY`
- `ZIP_BASE_URL`
- `ZIP_ACTUAL_BUDGET_CONFIG_ID`

The installer creates an `.env` file in the installed application folder with these values and the default entries:

- `VERIFY_SSL=true`
- `DEBUG=false`

Runtime dependencies are bundled into `NDR Zip Tool.exe` during the PyInstaller build. The installer does not install Python packages on the user's machine.

## GUI Buttons

- **Run Verification**: runs `verify_budget.py`.
- **Update Vendor Names**: launches interactive vendor name matching and, when not in Development Mode, sends selected Zip PATCH requests via `name_parser.py`.
- **Update vendor_list.txt**: when checked, refreshes `vendor_list.txt` from Zip before processing vendor name matches.
- **Add Budget Actuals**: reads actuals from the workbook and synchronizes them to Zip via `budget_actual.py`.
- **Exclude Vendors from vendor_exclusion.txt**: when checked, skips vendors listed in `vendor_exclusion.txt`.
- **Development Mode**: when enabled, the app runs selected workflows with `--debug` and `--dry-run` where available, and avoids passing `--apply` for vendor-name updates.
- **Top vendor count**: optional number of vendors to include in the Top Vendors analysis. Leave blank to skip the Top Vendors sheet update and Summary top vendor bar chart.
- **Clear Output**: clears the on-screen run log.
- **Open Workbook**: opens `vendor_budget.xlsx`.
- **Open Folder**: opens this project folder.
- **Preview Charts**: shows a simple in-app preview of Summary chart data after a run.
- **README**: opens this help file inside the app.

### Uninstalling on Windows

1. Open **Settings**.
2. Select **Apps > Installed apps**.
3. Find **NDR Zip Tool**.
4. Open the three-dot menu, select **Uninstall**, and follow the prompts.

The per-user installation normally does not require administrator permission to uninstall. The `.env` file or installation directory may remain because it contains user-created configuration. To remove it manually, press **Win+R**, enter the following path, and delete the folder:

```text
%LOCALAPPDATA%\Programs\NDR Zip Tool
```

### Design Limitations
Name Matching precision from the vendor_budget.xlsx to Zip is limited by the inputs into vendor_budget.xlsx. The code attempts to match in lieu of legal suffixes such as: 
```text
    'ag',
    'co',
    'company',
    'corp',
    'corporation',
    'gmbh',
    'inc',
    'incorporated',
    'limited',
    'llc',
    'llp',
    'lp',
    'ltd',
    'pa',
    'pc',
    'plc'
```
