# SAP-ABAP-ALV-Excel-Export
SAP ABAP ALV Report with one-click Excel export
REPORT z_emp_alv_varnika.

TABLES: pa0001, pa0002.

TYPES: BEGIN OF ty_emp,
         pernr TYPE pa0002-pernr,  "Employee Number
         vorna TYPE pa0002-vorna,  "First Name
         nachn TYPE pa0002-nachn,  "Last Name
         gbdat TYPE pa0002-gbdat,  "Date of Birth
         werks TYPE pa0001-werks,  "Personnel Area
         btrtl TYPE pa0001-btrtl,  "Personnel Subarea
       END OF ty_emp.

DATA: it_emp TYPE TABLE OF ty_emp,
      wa_emp TYPE ty_emp.

DATA: gt_fcat TYPE slis_t_fieldcat_alv,
      gs_layout TYPE slis_layout_alv,
      gs_fcat TYPE slis_fieldcat_alv.

* Selection Screen
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE TEXT-001.
SELECT-OPTIONS: s_pernr FOR pa0002-pernr,
                s_werks FOR pa0001-werks.
SELECTION-SCREEN END OF BLOCK b1.

START-OF-SELECTION.
  PERFORM get_data.
  PERFORM build_fieldcat.
  PERFORM display_alv.

*&---------------------------------------------------------------------*
*&      Form  GET_DATA
*&---------------------------------------------------------------------*
FORM get_data .
  IF it_emp IS INITIAL.
  "Dummy data for testing
  wa_emp-pernr = '100001'.
  wa_emp-vorna = 'Varnika'.
  wa_emp-nachn = 'Tyagi'.
  wa_emp-gbdat = '19990101'.
  wa_emp-werks = '1000'.
  wa_emp-btrtl = 'A001'.
  APPEND wa_emp TO it_emp.

  wa_emp-pernr = '100002'.
  wa_emp-vorna = 'Siya'.
  wa_emp-nachn = 'Jain'.
  wa_emp-gbdat = '19990102'.
  wa_emp-werks = '1002'.
  wa_emp-btrtl = 'A002'.
  APPEND wa_emp TO it_emp.
ENDIF.

  IF sy-subrc <> 0.
    MESSAGE 'No data found for given selection' TYPE 'I'.
    RETURN.
  ENDIF.
ENDFORM.

*&---------------------------------------------------------------------*
*&      Form  BUILD_FIELDCAT
*&---------------------------------------------------------------------*
FORM build_fieldcat .
  CLEAR gs_fcat.
  gs_fcat-fieldname = 'PERNR'.
  gs_fcat-seltext_m = 'Employee No'.
  gs_fcat-col_pos = 1.
  APPEND gs_fcat TO gt_fcat.

  gs_fcat-fieldname = 'VORNA'.
  gs_fcat-seltext_m = 'First Name'.
  gs_fcat-col_pos = 2.
  APPEND gs_fcat TO gt_fcat.

  gs_fcat-fieldname = 'NACHN'.
  gs_fcat-seltext_m = 'Last Name'.
  gs_fcat-col_pos = 3.
  APPEND gs_fcat TO gt_fcat.

  gs_fcat-fieldname = 'GBDAT'.
  gs_fcat-seltext_m = 'DOB'.
  gs_fcat-col_pos = 4.
  APPEND gs_fcat TO gt_fcat.

  gs_fcat-fieldname = 'WERKS'.
  gs_fcat-seltext_m = 'Personnel Area'.
  gs_fcat-col_pos = 5.
  APPEND gs_fcat TO gt_fcat.

  gs_fcat-fieldname = 'BTRTL'.
  gs_fcat-seltext_m = 'Sub Area'.
  gs_fcat-col_pos = 6.
  APPEND gs_fcat TO gt_fcat.
ENDFORM.

*&---------------------------------------------------------------------*
*&      Form  DISPLAY_ALV
*&---------------------------------------------------------------------*
FORM display_alv .
  gs_layout-zebra = 'X'. "Zebra pattern
  gs_layout-colwidth_optimize = 'X'. "Auto width
  gs_layout-box_fieldname = ''.

CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
EXPORTING
*   I_INTERFACE_CHECK                 = ' '
*   I_BYPASSING_BUFFER                = ' '
*   I_BUFFER_ACTIVE                   = ' '
   I_CALLBACK_PROGRAM                = sy-repid
*   I_CALLBACK_PF_STATUS_SET          = ' '
*   I_CALLBACK_USER_COMMAND           = ' '
*   I_CALLBACK_TOP_OF_PAGE            = ' '
*   I_CALLBACK_HTML_TOP_OF_PAGE       = ' '
*   I_CALLBACK_HTML_END_OF_LIST       = ' '
*   I_STRUCTURE_NAME                  =
*   I_BACKGROUND_ID                   = ' '
*   I_GRID_TITLE                      =
*   I_GRID_SETTINGS                   =
   IS_LAYOUT                         = gs_layout
   IT_FIELDCAT                       = gt_fcat
*   IT_EXCLUDING                      =
*   IT_SPECIAL_GROUPS                 =
*   IT_SORT                           =
*   IT_FILTER                         =
*   IS_SEL_HIDE                       =
*   I_DEFAULT                         = 'X'
*   I_SAVE                            = ' '
*   IS_VARIANT                        =
*   IT_EVENTS                         =
*   IT_EVENT_EXIT                     =
*   IS_PRINT                          =
*   IS_REPREP_ID                      =
*   I_SCREEN_START_COLUMN             = 0
*   I_SCREEN_START_LINE               = 0
*   I_SCREEN_END_COLUMN               = 0
*   I_SCREEN_END_LINE                 = 0
*   I_HTML_HEIGHT_TOP                 = 0
*   I_HTML_HEIGHT_END                 = 0
*   IT_ALV_GRAPHICS                   =
*   IT_HYPERLINK                      =
*   IT_ADD_FIELDCAT                   =
*   IT_EXCEPT_QINFO                   =
*   IR_SALV_FULLSCREEN_ADAPTER        =
* IMPORTING
*   E_EXIT_CAUSED_BY_CALLER           =
*   ES_EXIT_CAUSED_BY_USER            =
  TABLES
    T_OUTTAB                          = it_emp
 EXCEPTIONS
  PROGRAM_ERROR                     = 1
  OTHERS                            = 2.
ENDFORM.
