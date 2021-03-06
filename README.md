# IDEA-Script
Saint Xu's personal code

'-------------------------------------------Read Me-------------------------------------------------

‘This code is a new automation script for the IDEA application that assists engagement teams in performing JE testing.
’It simplifies the JE testing process and removes certain time-consuming procedures. 
‘Features:
‘1.Perform upfront preliminary health check on the journal entries listing automatically and instantly
’2.Perform completeness test automatically and generate the results instantly
‘3.Perform six recommended common pre-screening routines automatically and provide user-friendly journal characteristics selection screen ’for further screening on top of pre-screening
‘4.Visualise the pre-screened results through PowerBI in which pre-screened results are analysed from various dimensions, in particular ’with an analysis of journals hitting multiple routines
‘5.Show summary results of the number of journal entries under the selected criteria in further screening immediately
’6.Populate information inputted/decision made during the 5-step process and details of journal entries selected for testing onto the work ‘paper template
‘7.Set out key concepts of JE testing in the guidance buttons 


'-------------------------------------------Script-------------------------------------------------


'--------------------------↓Defining Variables----------------------


	
Public Const LOCALE_SSHORTDATE = &H1F       '  short date format string
Public Const LOCALE_SDATE = &H1D            '  date separator
Public Const LOCALE_SYSTEM_DEFAULT& = &H800
Public Const LOCALE_USER_DEFAULT& = &H400


Dim sFilename As String 
Dim sTempExcelSource As String 
Dim sFilename1 As String 
Dim Filename As String

Dim WSHnet As Object 
Dim UserName  As String 
Dim UserDomain As String  
Dim UserFullName As String 

Dim sProjectFolder  As String
Dim SQLconnStr As String
Dim SQLeqn As String
Dim SQLobjConn, SQLsfT As Object
Dim SQLrs As Object


Dim sType As String
Dim sDecimals As String
Dim sLen As String
Dim sMsg As String
Dim sTemp As String
Dim amountTotal As Double
Dim amountTotal1 As Double
Dim S1Check As Integer

Dim FieldArray_date()
Dim FieldArray_Char()
Dim FieldArray_mix()

Dim FurtherCriteria1 As String, FurtherCriteria2 As String, FurtherCriteria3 As String


'-----------------------------------项目基本信息变量-----------------------------------

Dim sEngagement_Info As String
Dim sPeriod_Start_Date As String
Dim sPeriod_End_Date As String
Dim sEnd_of_Reporting_Period_Start_Date As String


'-----------------------------------PBC文件变量-----------------------------------

Dim GL_File_name As String
Dim TB_File_Name As String

Dim  AccountMapping_File_name  As String

'-----------------------------------TB列名变量-----------------------------------

Dim	ACCOUNT_NUM_TB  As String
Dim	ACCOUNT_DESCRIPTION_TB  As String
Dim	Opening_Balance_TB As String
Dim	Ending_Balance_TB As String
Dim Total_Debit_TB As String, Total_Credit_TB As String

Dim 	Diff 	As String


'-----------------------------------GL列名变量-----------------------------------
Dim  Amount_JE As String
Dim Description_JE  As String
Dim DOCUMENT_NUM_JE As String
Dim ACCOUNT_NUM_JE As String
Dim ACCOUNT_DESCRIPTION_JE As String
Dim POSTING_DATE_JE As String   
Dim Dr_Amount_JE As String, Cr_Amount_JE As String 

Dim UESR_JE As String,IS_MANUAL_JE As String 

Dim IDEA_DEBIT_AMOUNT_JE As String, IDEA_CREDIT_AMOUNT_JE As String


'-----------------------------------过程中新增数据库变量-----------------------------------

Dim 	Null_GL_Account_IDM  As String 
Dim	Null_GL_Number_IDM As String
Dim	Null_GL_Description_IDM As String
Dim	NotinPeriod_PostDate_IDM As String
Dim	List_of_accounts_with_variance_IDM As String
Dim	Null_TB_account_number_IDM As String
Dim 	Null_TB_account_name_IDM As String
Dim	Duplicate_AccountCode_TB_IDM  As String


Dim DB_Name03_GLwithAccountMapping As String, DB_Name04_SampleGL As String
Dim DB_Name_JE01_01 As String
Dim DB_Name_JE02_01 As String, DB_Name_JE02_02 As String,DB_Name_A_JE02_01 As String, DB_Name_A_JE02_02 As String
Dim DB_Name_JE03_01 As String, DB_Name_JE03_02 As String, DB_Name_JE03_03 As String, DB_Name_JE03_04 As String
Dim DB_Name_A_JE03_1_01 As String,DB_Name_A_JE03_1_02 As String,DB_Name_A_JE03_1_03 As String,DB_Name_A_JE03_1_04 As String
Dim DB_Name_A_JE03_2_01 As String,DB_Name_A_JE03_2_02 As String,DB_Name_A_JE03_2_03 As String,DB_Name_A_JE03_2_04 As String
Dim DB_Name_JE04_01 As String, DB_Name_JE04_02 As String,DB_Name_A_JE04_01 As String, DB_Name_A_JE04_02 As String
Dim DB_Name_JE05_01 As String, DB_Name_JE05_02 As String
Dim DB_Name_JE06_01 As String, DB_Name_JE06_02 As String

Dim COMB_ACCOUNT_DESC As String
Dim ACCOUNT_NUM_AM As String, STANDARDIZED_ACCOUNT_NAME_AM As String
Dim JE04_ENDING_NUM_JE As String
Dim Criteria_JE02 As String, Formula_JE02 As String,Criteria_A_JE02 As String, Formula_A_JE02 As String
Dim Criteria_A_JE03_1 As String, Formula_A_JE03_1 As String,Criteria_A_JE03_2 As String, Formula_A_JE03_2 As String,Criteria_A_JE03_1_DR，Criteria_A_JE03_1_CR,Criteria_A_JE03_2_DR，Criteria_A_JE03_2_CR
Dim Criteria_A_JE04 As String, Formula_A_JE04 As String
Dim Delimiter As String
Dim PRESCR_R1 As String, PRESCR_R2 As String, PRESCR_R3  As String, PRESCR_R4 As String, PRESCR_A2  As String, PRESCR_A3_1 As String, PRESCR_A3_2 As String, PRESCR_A4 As String
Dim DB_NAME_BI_01 As String, DB_NAME_BI_02 As String, DB_NAME_BI_03 As String,DB_NAME_BI_04 As String, DB_NAME_BI_05 As String, DB_NAME_BI_06 As String,DB_NAME_BI_07 As String,DB_NAME_BI_FinalPopulation As String,DB_NAME_BI_FinalPeriod As String
Dim FIELD_NAME_BI_DOCUMENT_NUM_JE As String,FIELD_NAME_BI_POSTING_DATE_JE As String,FIELD_NAME_BI_CREATE_BY_JE As String,FIELD_NAME_BI_ACCOUNT_NUM_JE As String,FIELD_NAME_BI_ACCOUNT_DESC_JE As String,FIELD_NAME_BI_AMOUNT_JE As String,FIELD_NAME_BI_DESC_JE As String,FIELD_NAME_BI_MANUAL_JE As String
Dim FIELD_NAME_BI_Enagement_Info As String,FIELD_NAME_BI_Period_Start As String,FIELD_NAME_BI_Period_End As String,FIELD_NAME_BI_Period_Lastdate As String,FIELD_NAME_BI_Population As String
Dim AccountMapping_FileforGroup As String
Dim AccountMapping As String



Dim Completeness_Check_IDM As String
Dim DB_Name00_GLwithAccName As String, DB_Name01_SumJEbyAccNo As String, DB_Name02_CompletenessTest As String

Dim FurtherCriteria As String

Dim i As Variant
Dim k As Variant
Dim t As Variant
Dim F As String
Dim d As Variant
Dim arr1(99)

Dim Rerunstep As String


Dim WorkingDirectory As String
Dim DataWorkingDirectory As String
Dim LibraryWorkingDirectory As String
Dim RunAtServer As String
Dim WorkingDirectoryexport As String

'--------------------------↑End of defining variables----------------------









'■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■Start for KDC JE Script■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

Sub KDC_JE_Script


'================================================↓请在此段进行Mapping'====================================================

'========================================↓项目基本信息=========================


	sEngagement_Info = iSplit(Client.WorkingDirectory(),"\","\",1,1)		'已默认项目名称为IDEA Project Name，如需修改，则在下面那行的""中输入，如："万科2018年JE测试"
	'sEngagement_Info = "可以在此处自定义Report导出来的公司名称"			'可以在此处自定义Report导出来的公司名称

	sPeriod_Start_Date = "20180101"							'会计期间开始日，格式YYYYMMDD
	sPeriod_End_Date = "20180930"							'会计期间结束日，格式YYYYMMDD
	sEnd_of_Reporting_Period_Start_Date="20180901"					'报告期末开始日，格式YYYYMMDD

	
'=========================================↓Map数据库=========================================

	
	GL_File_name = "GL-GL.IDM"					'序时账的IDM
	TB_File_Name  = "TB-TB.IDM"					'TB的IDM	
	
	FurtherCriteria = "Criteria"				'用于自动导出Further Criteria,此处用于声明database的前缀，如条件1-3分别为：Criteria1，Criteria2，Criteria3，则此处填写他们相同的前缀"Criteria"
	
	
'=========================================↓Map列名=========================================必须确保numeric和character格式正确！！
	
	DOCUMENT_NUM_JE = "凭证编号"			'序时账_凭证编号，必须为Character格式
	ACCOUNT_NUM_JE = "账户编号"			'序时账_科目代码，必须为Character格式
	ACCOUNT_DESCRIPTION_JE = "科目文本"		'序时账_科目名称，必须为Character格式
	DESCRIPTION_JE = "文本"				'序时账_摘要，必须为Character格式

	Dr_Amount_JE = ""				'序时账本身的借方发生额，如果GL本来没有借方金额，此处请保留一对双引号，如Dr_Amount_JE = ""
	Cr_Amount_JE = ""				'序时账本身的贷方发生额，如果GL本来没有贷方金额，此处请保留一对双引号，如Cr_Amount_JE = ""
		
	Amount_JE = "本币金额"			'序时账变动额,必须为Numeric格式
	POSTING_DATE_JE="过帐日期"			'过账日期，必须为Date格式
	
	ACCOUNT_NUM_TB = "总帐科目"			'科目余额表_科目代码，必须为Character格式
	ACCOUNT_DESCRIPTION_TB = "短文本"		'科目余额表_科目名称，必须为Character格式
	Opening_Balance_TB = "已结转余额"		'科目余额表_期初余额，必须为Numeric格式
	Ending_Balance_TB = "累计余额"			'科目余额表_期末余额，必须为Numeric格式
	
	Total_Debit_TB = "报表期间的借方余额"		'科目余额表_本期累计借方发生额，如果TB本来没有本期累计借方发生额，此处请保留一对双引号，如Total_Debit_TB= ""
	Total_Credit_TB = "报表期间的贷方余额"		'科目余额表_本期累计贷方发生额，如果TB本来没有本期累计贷方发生额，此处请保留一对双引号，如Total_Credit_TB = ""
			
	UESR_JE="预制人"				'如果GL本来没有制单人列，此处请保留一对双引号，如UESR_JE= ""。如有，该列必须为文本格式
	IS_MANUAL_JE="MANUAL_AUTO"			'如果GL本来没有列可用于区分手工分录，此处请保留一对双引号，如IS_MANUAL_JE= ""。如有，该列必须为文本的"1"来表示手工分录
	
	
'=========================================↓Map Criteria=========================================
	Criteria_A_JE02="调整"		'留空（如""）表示项目组没有填写Criteria。关键字之间用英文状态下的分号分隔,注意不需要在分号前后加空格
	Criteria_A_JE03_1="Revenue,Cash or Cash at Bank;Trade Receivables, Special"	'留空（如""）表示项目组没有填写Criteria。英文状态下的分号前是借方科目，借方科目间用英文状态下的逗号分隔。英文状态下的分号后是贷方科目，贷方科目间用英文状态下的逗号分隔。如"Cash or Cash at Bank,Trade Receivables;Revenue"	
	Criteria_A_JE03_2=""					'同上
	'Additional的JE03不支持反向查找，如非Cash，非收入类分录。请在Further Criteria中手动执行。
	Criteria_A_JE04="999"				'留空（如""）表示项目组没有填写Criteria。关键字之间用英文状态下的分号分隔,注意不需要在分号前后加空格

	
	
	
'=========================================↓Account Mapping自定义选项=========================================

	AccountMapping = "AccountMappingTable.IDM"				'取消该行注释，则以本年GL发生过的科目生成AccountMappingTable，方便Mapping。上下两行二选一
	'AccountMapping = "Completeness_Check.IDM"				'取消该行注释，则以TB和GL完整的科目生成AccountMappingTable，可确保科目完整。上下两行二选一

	
	COMB_ACCOUNT_DESC = "COMB_ACCOUNT_DESC"					'取消该行注释，则以TB的科目名称为主生成AccountMappingTable，上下两行二选一
	'COMB_ACCOUNT_DESC = "COMB_ACCOUNT_DESC_Alternative"		'取消该行注释，则以GL的科目名称为主生成AccountMappingTable，上下两行二选一		
	
	
	
'=========================================↓Group Scenario时才要map=========================================

	'AccountMapping_FileforGroup  = "C:\Users\saintxu\Documents\My IDEA Documents\IDEA Projects\KDC Script Test@1206\Exports.ILB\KDC Script Test@1206_20181217115111_AccountMapping.XLSX"
				'↑当将整个Script放进Group Scenario里执行时，可以在这里指定整个Group通用的Account Mapping Table的文件位置，就能避免run到每一家公司的时候都要选择一次Account Mapping Table上传。

	
'================================================↑Mapping 结束====================================================

	
	


	

'-------------------------------------------------------------------------------------------------------------------↓下面的不用Map------------------------------------------------------------------------------------------------------------------
	
	
	
	Completeness_Check_IDM = "Completeness_Check.IDM"	'Validation9	完整性测试的IDM	
	
	ACCOUNT_NUM_AM ="GL_NUMBER"
	STANDARDIZED_ACCOUNT_NAME_AM ="STANDARDIZED_ACCOUNT_NAME"
	
	diff = "Completeness_Test"			'差异列
	
	AccountMapping_File_name="AccountMapping-Database.IDM"
	
	Null_GL_Account_IDM  = "Validation1.IDM"             		'1 	会计分录中科目代码为空
	Null_GL_Number_IDM = "Validation2.IDM"			'2	会计分录中凭证号为空
	Null_GL_Description_IDM = "Validation3.IDM"		'3	会计分录中凭证描述为空
	NotinPeriod_PostDate_IDM = "Validation4.IDM"		'4	记账日期不在会计期间
	List_of_accounts_with_variance_IDM = "Validation5.IDM"	'5	借贷不平的会计分录
	Null_TB_account_number_IDM = "Validation6.IDM"		'6	试算平衡表中科目号为空
	Null_TB_account_name_IDM= "Validation7.IDM"		'7	试算平衡表中科目名称为空
	Duplicate_AccountCode_TB_IDM = "Validation8.IDM"		'8	试算平衡表中科目代码重复
	
	
	DB_Name00_GLwithAccName = "GL with acc name.IDM"
	DB_Name01_SumJEbyAccNo = "Summary JE by acc no.IDM"
	DB_Name02_CompletenessTest = "Completeness_Check.IDM"
	
	
	
	IDEA_DEBIT_AMOUNT_JE="DEBIT_AMOUNT_JE"
	IDEA_CREDIT_AMOUNT_JE="CREDIT_AMOUNT_JE"
	DB_Name03_GLwithAccountMapping="GL with account mapping.IDM"
	DB_Name04_SampleGL="GL posted on and after the start date of the end of the reporting period.IDM"
	
	DB_Name_JE01_01="JE01 Manual journal entries posted on or after period end date.IDM"						'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	
	Criteria_JE02="Adj;Rev;Reclass;Sundry;Suspense;Error;Wrong;调整;冲回;冲销;重分类;核销;对冲;重述;错误;计划外;预算外;調整;沖回;沖銷;重分類;核銷;對沖;重述;錯誤;計劃外;預算外"	
	DB_Name_JE02_01="JE02 Records of journal entries with specific comments.IDM"
	DB_Name_JE02_02="JE02 Listing of journal entries with specific comments.IDM"		
	DB_Name_A_JE02_01="A_JE02 Records of journal entries with specific comments.IDM"
	DB_Name_A_JE02_02="A_JE02 Listing of journal entries with specific comments.IDM"						'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	
	DB_Name_JE03_01="JE03 Records of journal entries with CR Revenue.IDM"
	DB_Name_JE03_02="JE03 Listing of journal entries with CR Revenue.IDM"
	DB_Name_JE03_03="JE03 Records of journal entries with DR Others.IDM"
	DB_Name_JE03_04="JE03 Potential unexpected journal entries.IDM"									'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	DB_Name_A_JE03_1_01="A_JE03.1 Records of journal entries with CR .IDM"
	DB_Name_A_JE03_1_02="A_JE03.1 Listing of journal entries with CR .IDM"
	DB_Name_A_JE03_1_03="A_JE03.1 Records of journal entries with DR .IDM"
	DB_Name_A_JE03_1_04="A_JE03.1 Potential unexpected journal entries.IDM"								'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	DB_Name_A_JE03_2_01="A_JE03.2 Records of journal entries with CR .IDM"
	DB_Name_A_JE03_2_02="A_JE03.2 Listing of journal entries with CR .IDM"
	DB_Name_A_JE03_2_03="A_JE03.2 Records of journal entries with DR .IDM"
	DB_Name_A_JE03_2_04="A_JE03.2 Potential unexpected journal entries.IDM"								'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	
	JE04_ENDING_NUM_JE="ENDING_NUM"
	DB_Name_JE04_01="JE04 Records of journal entries with rounded amount.IDM"
	DB_Name_JE04_02="JE04 Listing of journal entries with rounded amount.IDM"							'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	DB_Name_A_JE04_01="A_JE04 Records of journal entries with rounded amount.IDM"
	DB_Name_A_JE04_02="A_JE04 Listing of journal entries with rounded amount.IDM"							'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
		
	DB_Name_JE05_01="JE05 Summary JE by user.IDM"
	DB_Name_JE05_02="JE05 A list of number of journal entries posted by user.IDM"							'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	
	DB_Name_JE06_01="JE06 Summary JE by acc no.IDM"
	DB_Name_JE06_02="JE06 A list of accounts with the number of postings.IDM"							'手动做了PreScreening，但是要自动导PreScreeningReport时，需要Map这个database（按SOP标准名称命名则不需要再修改）
	
	Delimiter=";"


' Power BI	
	PRESCR_R1="PRESCR_R1"
	PRESCR_R2="PRESCR_R2"
	PRESCR_R3="PRESCR_R3"
	PRESCR_R4="PRESCR_R4"
	PRESCR_A2="PRESCR_A2"
	PRESCR_A3_1="PRESCR_A3_1"
	PRESCR_A3_2="PRESCR_A3_2"
	PRESCR_A4="PRESCR_A4"	

	DB_NAME_BI_01="#DB#BI#1.IDM"
	DB_NAME_BI_02="#DBBI#2.IDM"
	DB_NAME_BI_03="#DB#BI#3.IDM"
	DB_NAME_BI_04="#DB#BI#4.IDM"
	DB_NAME_BI_05="#DB#BI#5.IDM"
	DB_NAME_BI_06="#DB#BI#6.IDM"
	DB_NAME_BI_07="#DB#BI#7.IDM"
	DB_NAME_BI_FinalPeriod="#GL#In_Period.IDM"
	DB_NAME_BI_FinalPopulation="#GL#_Population.IDM"

	FIELD_NAME_BI_DOCUMENT_NUM_JE="DOCUMENT_NUM_JE"
	FIELD_NAME_BI_POSTING_DATE_JE="POSTING_DATE_JE"
	FIELD_NAME_BI_CREATE_BY_JE="CREATE_BY_JE"
	FIELD_NAME_BI_ACCOUNT_NUM_JE="ACCOUNT_NUM_JE"
	FIELD_NAME_BI_ACCOUNT_DESC_JE="ACCOUNT_DESC_JE"
	FIELD_NAME_BI_AMOUNT_JE="AMOUNT_JE"
	FIELD_NAME_BI_DESC_JE="DESC_JE"
	FIELD_NAME_BI_MANUAL_JE="MANUAL_JE"
	FIELD_NAME_BI_Enagement_Info="ENGAGEMENT_INFO"
	FIELD_NAME_BI_Period_Start="PERIOD_START"	
	FIELD_NAME_BI_Period_End="PERIOD_END"
	FIELD_NAME_BI_Period_Lastdate="PERIOD_LASTDATE"
	FIELD_NAME_BI_Population="POPULATION"
	
'----------------------------↓各Template位置-------------------------------

	sTempExcelSource = "C:\temp\KDC JE Template"			'KDC JE Template 新摆放位置
	'sTempExcelSource = "C:\temp"

	WorkingDirectoryexport =  	client.WorkingDirectory &"Exports.ILB\"

	
'------------------------------------------------------------------------------------------------------↑上面的不用Map------------------------------------------------------------------------------------------------------------------	

	
'=========================================↓如需要在IDEA Server运行script，请取消以下六行注释=========================================


	RunAtServer = "true"
	Client.RunAtServer  true
	DataWorkingDirectory="\\cnfuoapp140\idea$\Current\Test for Script_12338f74-3bd8-48a7-b424-a732e4102ef3" & "\Data\"   			'请在这里将IDEA Server的Project路径粘贴在" "内
	LibraryWorkingDirectory=Left(DataWorkingDirectory,Len(DataWorkingDirectory)-5) & "Library\"
	WorkingDirectoryexport = LibraryWorkingDirectory & "exports\"
	sEngagement_Info =  iSplit(iSplit( LibraryWorkingDirectory,"_","_",1,1),"","\",1,1)							'已默认项目名称为IDEA Project Name，如需修改，请在"="后面替换，如：= "万科2018年JE测试"


	
'=========================================↓选择需要执行的模块=========================================

	
	'Call Step1·1_Validation1to8               		'执行Validation Check	
	
	'Call Step1·2_Completeness_Check			'执行完整性测试，执行完请手动Append差异列的计算公式

								'Step1·2·1 请手动append差异列公式
		
	Call Step1·3_Export_Validation	       		'导出Validation Report
	
	Call Step2·1_Export_AccountMapFile			'导出AccountMappingTable
	
								'Step2·1·1 请让项目组填写AccountMappingTable
	
	'Call Step2·2_Upload_AccountMapping_File		'导入AccountMappingTable
		
	'Call Step3·1_Pre_Screening				'手动修改Additional Criteria，执行PreScreening
	
	'Call Step3·2_Export_Prescreening			'导出Pre-screening Report
	
	'Call Step3·3_Export_Power_BI						'导出PowerBI的数据源及Template
		
	'Call Step4_Export_Further_Criteria			'导出Further Criteria Report
	
	
'=========================================↑选择需要执行的模块=========================================

'=========================================↓选择需要ReRun的模块=========================================

	
	'Call Rerun_Step1_Validation
	
	'Call Rerun_step1_Completeness_Check
	
	'Call Rerun_Step2_AccountMapping
	
	'Call Rerun_Step3_Pre_Screening
	
	
'=========================================↑选择需要ReRun的模块=========================================

	
	 	Client.RefreshFileExplorer		'刷新Database列表，请勿修改
		client.closeall
		
	 	MsgBox "Done"				'Group Scenario时请注释掉该行
	 	
'=========================================↑Tailor made结束=========================================
	 	
End Sub 

'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------
'------------------------------------------------------------------------------------------------------------------------------------------KDC JE Script------------------------------------------------------------------------------------------------------------------------------------------


'■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■End of KDC JE Script■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■





















Function GetTotal(DBname As String, fieldname1 As String, TotalType As String)
	If fieldname1 = "" Then
		Set db = Client.OpenDatabase(DBname)
		Select Case TotalType 
			Case "DBCount"
			GetTotal = db.Count  
		End Select
		Set db = Nothing
	 Else
		Set db = Client.OpenDatabase(DBname)
		Set stats = db.FieldStats(fieldname1)
		Select Case TotalType 	
			Case "NetValue"
				GetTotal =  Abs( stats.NetValue() )
			Case "PositiveValue"
				GetTotal = stats.DrValue()
			Case "NegativeVaule"
				GetTotal = stats.CrValue()
			Case "MaxValue"
				GetTotal = stats.MaxValue()
			Case "AverageValue"
				GetTotal = stats.AvgValue()
		End Select
		Set stats = Nothing
		Set db = Nothing
	End If
End Function

Function GetJENumber (DBname As String, fieldname2 As String)

			Set db = Client.OpenDatabase(DBname)
			Set task = db.Summarization
			task.AddFieldToSummarize fieldname2
			task.OutputDBName = "Temp-Summarization.IDM"
			task.CreatePercentField = FALSE
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase ("Temp-Summarization.IDM")		
		
		GetJENumber =  GetTotal("Temp-Summarization.IDM","" ,"DBCount" )
			If GetJENumber >2500 Then
				GetJENumber = ">2500"
			End If
			
			Fun_Delete_File("Temp-Summarization.IDM")

			' Clear the memory.

			Set db = Nothing
			
			Set stats = Nothing
			

End Function

Function GetFieldStatistic  (DBname As String, fieldname1 As String, StatType As String)


' Open the database.


Set db = Client.OpenDatabase(DBname)

' Get the field statistics.

Set stats = db.FieldStats(fieldname1)

' Obtain the number of categories.

Select Case StatType
	
	
	Case "NOofCategories"
	
	GetFieldStatistic = stats.NumCategories()
	
	Case "NOofBlanks"
	
	GetFieldStatistic  = stats.NumBlanks()
	
End Select
	
' Clear the memory.

Set db = Nothing

Set stats = Nothing

End Function







Function ExportDatabaseXLSX(sSourceDB As String, sExcelName As String, sSheetName As String)
	Set db = Client.OpenDatabase(sSourceDB)
	Set task = db.ExportDatabase
	task.IncludeAllFields
	eqn = ""
	
	If	RunAtServer = "true" Then
	
		task.PerformTask   sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
		
	Else

		task.PerformTask  workingdirectoryexport + sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
	End If

	Set db = Nothing
	Set task = Nothing
End Function

Function ExportDatabaseXLSXforJE06(sSourceDB As String, sExcelName As String, sSheetName As String)
	Set db = Client.OpenDatabase(sSourceDB)
	Set task = db.ExportDatabase
	task.AddFieldToInc ACCOUNT_NUM_JE
	task.AddFieldToInc ACCOUNT_DESCRIPTION_JE
	task.AddFieldToInc "NO_OF_RECS"
	task.AddFieldToInc Amount_JE & "_SUM"
	task.AddFieldToInc IDEA_DEBIT_AMOUNT_JE & "_SUM"
	task.AddFieldToInc IDEA_Credit_AMOUNT_JE & "_SUM"
	eqn = ""
	If	RunAtServer = "true" Then
	
		task.PerformTask   sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
		
	Else

		task.PerformTask  workingdirectoryexport + sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
	End If

	Set db = Nothing
	Set task = Nothing
End Function

Function ExportDatabaseXLSXwithIndex (sSourceDB As String, sExcelName As String, sSheetName As String, sField As String, R As String)
	Set db = Client.OpenDatabase(sSourceDB)
	Set task = db.ExportDatabase
	task.IncludeAllFields
	task.addkey sField, R
	eqn = ""
	If	RunAtServer = "true" Then
	
		task.PerformTask   sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
		
	Else

		task.PerformTask  workingdirectoryexport + sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
	End If

	Set db = Nothing
	Set task = Nothing
End Function

Function ExportDatabaseXLSXwithIndexforJE0104 (sSourceDB As String, sExcelName As String, sSheetName As String)
	Set db = Client.OpenDatabase(sSourceDB)
	Set task = db.ExportDatabase
	task.IncludeAllFields
	task.AddKey POSTING_DATE_JE, "D"
	task.AddKey DOCUMENT_NUM_JE, "D"
	eqn = ""
	If	RunAtServer = "true" Then
	
		task.PerformTask   sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
		
	Else

		task.PerformTask  workingdirectoryexport + sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
	End If

	Set db = Nothing
	Set task = Nothing
End Function



Function ExportDatabaseGLXLSX(sSourceDB As String, sExcelName As String, sSheetName As String)

	Set db = Client.OpenDatabase(sSourceDB)
	Set task = db.ExportDatabase

	task.AddFieldToInc DOCUMENT_NUM_JE
	task.AddFieldToInc ACCOUNT_NUM_JE
	task.AddFieldToInc ACCOUNT_DESCRIPTION_JE
	task.AddFieldToInc DESCRIPTION_JE
	'task.AddFieldToInc Dr_Amount_JE
	'task.AddFieldToInc Cr_Amount_JE
	task.AddFieldToInc Amount_JE
	task.AddFieldToInc POSTING_DATE_JE
	'task.AddFieldToInc IDEA_DEBIT_AMOUNT_JE
	'task.AddFieldToInc IDEA_CREDIT_AMOUNT_JE

	eqn = ""
	
	If	RunAtServer = "true" Then
	
		task.PerformTask   sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
		
	Else

		task.PerformTask  workingdirectoryexport + sExcelName , sSheetName , "XLSX", 1, db.Count, eqn
	End If

	Set db = Nothing
	Set task = Nothing
	
End Function


Function Fun_File_Exist(sIDMFile As String) As Boolean

	Set fs = CreateObject("Scripting.FileSystemObject")
	
	If  fs.FileExists(DataWorkingDirectory & sIDMFile ) Then 
		Fun_File_Exist = true
	Else
		Fun_File_Exist = false
	End If
	
	fs = Nothing

End Function


Function Fun_Delete_File(sIDMFile As String)

	Set fs = CreateObject("Scripting.FileSystemObject")
	
	If  fs.FileExists(DataWorkingDirectory & sIDMFile ) Then 
		Client.CloseDatabase sIDMFile
		Client.DeleteDatabase sIDMFile
	End If
	
	fs = Nothing

End Function



Function GetFile
	Dim obj As Object 
	Set obj = Client.CommonDialogs
	sfilename = obj.FileExplorer()
	Set obj = Nothing
End Function


Function step1·1_Validation1to8

Call checkValidationFields
		
'1
	Set db = Client.OpenDatabase (GL_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_GL_Account_IDM
	task.AddExtraction dbName, "", "" & ACCOUNT_NUM_JE  & "  = """""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
'2
	Set db = Client.OpenDatabase (GL_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_GL_Number_IDM
	task.AddExtraction dbName, "", "" & DOCUMENT_NUM_JE  & "  = """""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)


'3
	Set db = Client.OpenDatabase (GL_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_GL_Description_IDM
	task.AddExtraction dbName, "", "" & Description_JE & "  = """""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
'4

	
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = NotinPeriod_PostDate_IDM
	task.AddExtraction dbName, "",  """" & sPeriod_End_Date & """ < " & POSTING_DATE_JE & " .OR.  " & POSTING_DATE_JE & "  < """& sPeriod_Start_Date  & """"
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	

'5
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.Summarization
	task.AddFieldToSummarize DOCUMENT_NUM_JE    ' "STD_唯一凭证号"
	task.AddFieldToTotal Amount_JE    ' "STD_序时账发生额"
	dbName = "Summary by Doc No.IDM"
	task.OutputDBName = dbName
	task.CreatePercentField = FALSE
	task.StatisticsToInclude = SM_SUM
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
	Set db = Client.OpenDatabase("Summary by Doc No.IDM")
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = List_of_accounts_with_variance_IDM
	task.AddExtraction dbName, "", ""&Amount_JE & "_SUM" & " <> 0" 
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
'6

	Set db = Client.OpenDatabase (TB_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_TB_account_number_IDM
	task.AddExtraction dbName, "", "" & ACCOUNT_NUM_TB & "  = """""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

	

'7

	If ACCOUNT_DESCRIPTION_TB = "" Then				'如本身无TB科目名称列，则整个Tb都有问题
	

	Set db = Client.OpenDatabase (TB_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_TB_account_name_IDM
	task.AddExtraction dbName, "", ""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

	
	Else
	
	
	Set db = Client.OpenDatabase (TB_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = Null_TB_account_name_IDM
	task.AddExtraction dbName, "", "" & ACCOUNT_DESCRIPTION_TB & "  = """""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
	End If
	
'8
	Set db = Client.OpenDatabase(TB_File_Name)
	Set task = db.DupKeyDetection
	task.IncludeAllFields
	task.AddKey ACCOUNT_NUM_TB, "A"
	task.OutputDuplicates = TRUE
	dbName = Duplicate_AccountCode_TB_IDM 
	task.CreateVirtualDatabase = False
	task.PerformTask dbName, ""
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)	
	

End Function


Function step1·2_Completeness_Check

Call checkValidationFields    '检查必要列


	
' Remove Field删除IDEA Script自动增加的列
	Set db = Client.OpenDatabase(GL_File_name)
	Set table = db.TableDef 
	Set task = db.TableManagement
	For i = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
		Set field = table.GetFieldAt(i) 
		If field.name = IDEA_DEBIT_AMOUNT_JE Or field.name = IDEA_CREDIT_AMOUNT_JE Then
			task.RemoveField field.name
			task.PerformTask	
		End If
	Next i	
	Set field = Nothing
	Set table = Nothing	
	Set task = Nothing
	Set db = Nothing


	
' Append Field 新增一列借正贷负的IDEA_DEBIT_AMOUNT
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	field.Name = IDEA_DEBIT_AMOUNT_JE
	field.Description = "Calculate debit amount in GL"
	field.Type = WI_VIRT_NUM
	field.Equation = "@If( " & Amount_JE & "> 0, " & Amount_JE & ",0)"
	field.Decimals = 2
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing

' Append Field 新增一列借正贷负的IDEA_CREDIT_AMOUNT
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	field.Name = IDEA_CREDIT_AMOUNT_JE
	field.Description = "Calculate credit amount in GL"
	field.Type = WI_VIRT_NUM
	field.Equation = "@If(" & Amount_JE & " < 0," & Amount_JE & " ,0)"
	field.Decimals = 2
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing


' Analysis: Summarization 
	Set db = Client.OpenDatabase(GL_File_Name)
	Set task = db.Summarization
	task.AddFieldToSummarize ACCOUNT_NUM_JE
	task.AddFieldToInc ACCOUNT_DESCRIPTION_JE
	task.AddFieldToTotal IDEA_DEBIT_AMOUNT_JE
	task.AddFieldToTotal IDEA_CREDIT_AMOUNT_JE
	If Dr_Amount_JE<>""  Then		'当GL本身没有借方金额的时候，不用Sum这一列
		task.AddFieldToTotal Dr_Amount_JE
	End If
	If Cr_Amount_JE<>"" Then		'当GL本身没有贷方金额的时候，不用Sum这一列
		task.AddFieldToTotal Cr_Amount_JE	
	End If
	task.AddFieldToTotal Amount_JE 
	dbName = DB_Name01_SumJEbyAccNo
	task.OutputDBName = dbName
	task.CreatePercentField = FALSE
	task.UseFieldFromFirstOccurrence = TRUE
	task.StatisticsToInclude = SM_SUM
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

' File: Join Databases
	Set db = Client.OpenDatabase(DB_Name01_SumJEbyAccNo)
	Set task = db.JoinDatabase
	task.FileToJoin TB_File_Name
	task.AddPFieldToInc ACCOUNT_NUM_JE
	task.AddPFieldToInc ACCOUNT_DESCRIPTION_JE
	task.AddPFieldToInc Amount_JE & "_SUM"
	task.AddPFieldToInc IDEA_DEBIT_AMOUNT_JE & "_SUM"
	task.AddPFieldToInc IDEA_CREDIT_AMOUNT_JE & "_SUM"
	If Dr_Amount_JE<>"" Then						'当GL本身没有借方金额的时候，不用Join这一列
		task.AddPFieldToInc Dr_Amount_JE & "_SUM"
	End If
	If Cr_Amount_JE<>"" Then 						'当GL本身没有贷方金额的时候，不用Join这一列
		task.AddPFieldToInc Cr_Amount_JE & "_SUM"
	End If
	task.AddSFieldToInc ACCOUNT_NUM_TB
	If ACCOUNT_DESCRIPTION_TB <>"" Then		'当TB本身没有科目名称的时候，不用Join这一列
		task.AddSFieldToInc  ACCOUNT_DESCRIPTION_TB
	End If
	task.AddSFieldToInc  Opening_Balance_TB
	task.AddSFieldToInc  Ending_Balance_TB 
	If Total_Debit_TB <>"" Then						'当TB没有累计借方金额的时候，不用Join这一列
		task.AddSFieldToInc  Total_Debit_TB 
	End If
	If Total_Credit_TB <>"" Then						'当TB没有累计贷方金额的时候，不用Join这一列
		task.AddSFieldToInc  Total_Credit_TB 
	End If	
	task.AddMatchKey ACCOUNT_NUM_JE, ACCOUNT_NUM_TB, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name02_CompletenessTest
	task.PerformTask dbName, "", WI_JOIN_ALL_REC
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

	
' Append Field
'-------------------考虑GL和TB有相同field name的情况-----------------------------
	If ACCOUNT_NUM_JE = ACCOUNT_NUM_TB Then
		ACCOUNT_NUM_TB =ACCOUNT_NUM_TB &1
	End If
'----------------------------------------------------------------------------------------------------------------------------
	Set db = Client.OpenDatabase(DB_Name02_CompletenessTest)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	field.Name = "COMB_ACCOUNT_NUM"
	field.Description = "Combine the account number from TB and GL"
	field.Type = WI_VIRT_CHAR
	field.Equation = "@If(" & ACCOUNT_NUM_TB & " <>  """"," & ACCOUNT_NUM_TB  & ",  " & ACCOUNT_NUM_JE  &")"
	field.Length = 100
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing

	
' Append Field
'-------------------考虑GL和TB有相同field name的情况-----------------------------
	If ACCOUNT_DESCRIPTION_JE =ACCOUNT_DESCRIPTION_TB Then
		ACCOUNT_DESCRIPTION_TB =ACCOUNT_DESCRIPTION_TB &1
	End If
'----------------------------------------------------------------------------------------------------------------------------

'-------------------以TB的科目名称为主，GL的科目名称为补充-----------------------------

	Set db = Client.OpenDatabase(DB_Name02_CompletenessTest)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	field.Name = "COMB_ACCOUNT_DESC"
	field.Type = WI_VIRT_CHAR
	If ACCOUNT_DESCRIPTION_TB <> "" Then
		field.Description = "Obtain account name from TB, with GL's account name as subsitute"
		field.Equation = "@If(" & ACCOUNT_DESCRIPTION_TB & "<>  """", " & ACCOUNT_DESCRIPTION_TB & ", " & ACCOUNT_DESCRIPTION_JE  & ")"		
	Else
		field.Description = "Obtain account name from TB, with GL's account name as subsitute"
		field.Equation = ACCOUNT_DESCRIPTION_JE
	End If
	field.Length = 1000
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing
	
'-------------------以GL的科目名称为主，TB的科目名称为补充-----------------------------


	Set db = Client.OpenDatabase(DB_Name02_CompletenessTest)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	field.Name = "COMB_ACCOUNT_DESC_Alternative"
	field.Type = WI_VIRT_CHAR
	If ACCOUNT_DESCRIPTION_JE <> "" Then
		field.Description = "Obtain account name from GL, with TB's account name as subsitute"
		field.Equation = "@If(" & ACCOUNT_DESCRIPTION_JE & "<>  """", " & ACCOUNT_DESCRIPTION_JE & ", " & ACCOUNT_DESCRIPTION_TB  & ")"		
	Else
		field.Description = "Obtain account name mainly from GL, with TB's account name as subsitute"
		field.Equation = ACCOUNT_DESCRIPTION_TB
	End If
	field.Length = 1000
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing
	
'-------------------生成AccountMappingTable的数据库-----------------------------

	Set db = Client.OpenDatabase(	DB_Name01_SumJEbyAccNo	)
	Set task = db.JoinDatabase
	task.FileToJoin 	Completeness_Check_IDM
	task.IncludeAllPFields
	task.IncludeAllSFields
	task.AddMatchKey ACCOUNT_NUM_JE, ACCOUNT_NUM_JE, "A"
	task.CreateVirtualDatabase = False
	dbName = "AccountMappingTable.IDM"
		task.PerformTask dbName, "", WI_JOIN_ALL_IN_PRIM
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
	
	
	
'↓请在此插入append差异列的scrit，即可自动执行
	
	

	
	
	
	
End Function







Function Step1·3_Export_Validation

Call checkValidationFields

	Dim sTemp As String
	Dim db  As database
	Dim rs As recordset
	Dim ThisTable As Object
	Dim field As field
	Dim rec As Object
	Dim i As Long
	Dim j As Integer
	Dim iFieldCount As Integer
	Dim P As Integer
	
	Dim objUser As String 
	Set WSHnet = CreateObject("WScript.Network")
	Let UserName = WSHnet.UserName
	Let UserDomain = WSHnet.UserDomain
	On Error Resume Next	
	Set objUser = GetObject("WinNT://" & UserDomain & "/" & UserName & ",user")
	Let UserFullName = objUser.FullName
	
	If UserFullName = "" Then UserFullName = UserName 
	
	'sProjectFolder = client.WorkingDirectory
	
	strr= sTempExcelSource & "\ValidationReport.xlsx"   ' "Exports.ILB\ValidationReport.xlsx"
	dstr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_ValidationReport.xlsx"
	MsgBox dstr
	If Len( dstr ) > 218 Then
		MsgBox "您设置的Project名称太长，将导致生成的Report错误。请在Mapping处的[sEngagement_Info]中重新设定一个较短的名称再尝试"
		End
	Else
	End If
	
	
	FileCopy strr, dstr

	
	Set excel = CreateObject("Excel.Application")
	Set oBook = excel.Workbooks.Open(dstr)
	Set oSheet = oBook.Worksheets.Item("ValidationReport")


'---------------------------------------------------------------------------项目基本信息取值-----------------------------------------

	oSheet.Range("E1").value = "Client:" & sEngagement_Info
	oSheet.Range("E2").value = "Year End: " & sPeriod_End_Date 
	'oSheet.Range("E4").value = "Prepared by: " & UserFullName
	'oSheet.Range("E5").value = "Prepared date: " & Format(DateValue(Now),"YYYY/MM/DD")
	
	
'---------------------------------------------------------------------------Validation基本信息取值(9条基本信息)-----------------------------------------

	oSheet.Range("E9").value =  iSplit(GL_File_name,"","\",1,1)  'GL_Source_File_Name
	
	amountTotal = GetTotal( GL_File_name , Amount_JE, "NetValue")
	If amountTotal>0.01 Or amountTotal<-0.01  Then
		oSheet.Range("E10").value = amountTotal
	Else
		oSheet.Range("E10").value = 0
	End If 
	
	sTemp = GetTotal(GL_File_name , Amount_JE, "PositiveValue")
	oSheet.Range("E11").value = sTemp

	sTemp = GetTotal(GL_File_name , Amount_JE, "NegativeVaule")
	oSheet.Range("E12").value = sTemp


	sTemp = GetTotal(GL_File_name , "" , "DBCount")
	oSheet.Range("E13").value = sTemp

	oSheet.Range("E14").value =  TB_File_Name  'TB_Source_File_Name
	
	amountTotal  = GetTotal(TB_File_Name, Opening_Balance_TB, "NetValue")
	If amountTotal>0.01 Or amountTotal<-0.01  Then
		oSheet.Range("E15").value = amountTotal
	Else
		oSheet.Range("E15").value = 0
	End If 	

	amountTotal = GetTotal(TB_File_Name, Ending_Balance_TB, "NetValue")
	If amountTotal>0.01 Or amountTotal<-0.01  Then
		oSheet.Range("E16").value = amountTotal
	Else
		oSheet.Range("E16").value = 0
	End If 	

	sTemp = GetTotal(TB_File_Name  ,"" ,"DBCount" )
	oSheet.Range("E17").value = sTemp

'--------------------------↓此处为Validation Check 1-8的代码-----------------------------	



'1
	If  Fun_File_Exist(Null_GL_Account_IDM) Then 
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		sTemp = GetTotal(Null_GL_Account_IDM ,"" ,"DBCount" )
		oSheet.Range("D22").value = sTemp
		If sTemp > 0   Then
		
			oSheet.Range("D22").Interior.Color = RGB(255, 255, 0)
			
			strr = WorkingDirectoryExport & "#Null-GL_Account.xlsx"
			

			If sTemp > 1048576 then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
			Else
			
				Call ExportDatabaseGLXLSX(Null_GL_Account_IDM, "#Null-GL_Account.xlsx" , "V_Report 1")
			
			
				
				Set oSheet = oBook.Worksheets.Add
				oSheet.Name = "V_Report 1"
				Set oBook2=excel.Workbooks.Open(strr)
				Set oSheet2=oBook2.Worksheets.item("V_Report 1")		
				Set oRange=oSheet2.UsedRange
							oRange.Font.Name = "Arial"
				oRange.Font.size = 10
				oRange.Copy
				oSheet.Paste 
				oBook2.Save
				oBook2.Close (True)
				Kill strr 
		
				For i = 1 To  20
					oSheet.Columns(i).EntireColumn.AutoFit
				Next i
				oBook.Sheets("V_Report 1").Move After:=oBook.Sheets(oBook.Sheets.Count)
	
			End If
		
		Else 
		
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E22").value = "N/A"
					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E22").value = "N/A"
	
	End If
	
	
'2
	If  Fun_File_Exist(Null_GL_Number_IDM) Then 
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		sTemp = GetTotal(Null_GL_Number_IDM, "" ,"DBCount" )
		oSheet.Range("D23").value = sTemp
		If sTemp > 0   Then
			
			oSheet.Range("D23").Interior.Color = RGB(255, 255, 0)
			
			strr = WorkingDirectoryExport & "Null_GL_Number.xlsx"
			
						
			If Stemp > 1048576 Then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
			Else
			
			
				Call ExportDatabaseGLXLSX(Null_GL_Number_IDM , "Null_GL_Number.xlsx" , "V_Report 2")
				
				Set oSheet = oBook.Worksheets.Add
				oSheet.Name = "V_Report 2"
				Set oBook2=excel.Workbooks.Open(strr)
				Set oSheet2=oBook2.Worksheets.item("V_Report 2")		
				Set oRange=oSheet2.UsedRange
							oRange.Font.Name = "Arial"
				oRange.Font.size = 10
				oRange.Copy
				oSheet.Paste 
				oBook2.Save
				oBook2.Close (True)
				Kill strr 
		
				For i = 1 To  20
					oSheet.Columns(i).EntireColumn.AutoFit
				Next i
				
				oBook.Sheets("V_Report 2").Move After:=oBook.Sheets(oBook.Sheets.Count)				
			End If
			
		Else 
		
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E23").value = "N/A"

					
		End If
	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E23").value = "N/A"
	End If

	
'3
	If  Fun_File_Exist(Null_GL_Description_IDM) Then 
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		sTemp = GetTotal(Null_GL_Description_IDM,"" ,"DBCount" )
		oSheet.Range("D24").value = sTemp
		If sTemp > 0   Then
			
			oSheet.Range("D24").Interior.Color = RGB(255, 255, 0)
			
			strr = WorkingDirectoryExport & "#Null-GL_Description.xlsx"
			
						
			If Stemp > 1048576 then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
			Else
			
			
				Call ExportDatabaseGLXLSX(Null_GL_Description_IDM , "#Null-GL_Description.xlsx" , "V_Report 3")
				
				Set oSheet = oBook.Worksheets.Add
				oSheet.Name = "V_Report 3"
				Set oBook2=excel.Workbooks.Open(strr)
				Set oSheet2=oBook2.Worksheets.item("V_Report 3")		
				Set oRange=oSheet2.UsedRange
							oRange.Font.Name = "Arial"
				oRange.Font.size = 10
				oRange.Copy
				oSheet.Paste 
				oBook2.Save
				oBook2.Close (True)
				Kill strr 
		
				For i = 1 To  20
					oSheet.Columns(i).EntireColumn.AutoFit
				Next i
				
				oBook.Sheets("V_Report 3").Move After:=oBook.Sheets(oBook.Sheets.Count)
				
			End If

			
		Else 
		
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E24").value = "N/A"

					
		End If
	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")

		oSheet.Range("E24").value = "N/A"
	End If

	
'4
	If  Fun_File_Exist(NotinPeriod_PostDate_IDM) Then 
				
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		sTemp = GetTotal(NotinPeriod_PostDate_IDM ,"" ,"DBCount" )
		oSheet.Range("D25").value = sTemp
		If sTemp > 0   Then
			oSheet.Range("D25").Interior.Color = RGB(255, 255, 0)
			
			strr = WorkingDirectoryExport & "#NotinPeriod_PostDate.xlsx"
			
					
			If Stemp > 1048576 then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
			Else
			
			
				Call ExportDatabaseGLXLSX(NotinPeriod_PostDate_IDM, "#NotinPeriod_PostDate.xlsx" , "V_Report 4")
				
				Set oSheet = oBook.Worksheets.Add
				oSheet.Name = "V_Report 4"
				Set oBook2=excel.Workbooks.Open(strr)
				Set oSheet2=oBook2.Worksheets.item("V_Report 4")		
				Set oRange=oSheet2.UsedRange
							oRange.Font.Name = "Arial"
				oRange.Font.size = 10
				oRange.Copy
				oSheet.Paste 
				oBook2.Save
				oBook2.Close (True)
				Kill strr 
		
				For i = 1 To  20
					oSheet.Columns(i).EntireColumn.AutoFit
				Next i
				
				oBook.Sheets("V_Report 4").Move After:=oBook.Sheets(oBook.Sheets.Count)
			
			End If
			
		Else 
		
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E25").value = "N/A"
					
		End If

	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E25").value = "N/A"
	End If

'5
	If  Fun_File_Exist(List_of_accounts_with_variance_IDM) Then 
			
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		sTemp = GetTotal(List_of_accounts_with_variance_IDM ,"" ,"DBCount" )
		oSheet.Range("D26").value = sTemp
		If sTemp > 0  Then 
			oSheet.Range("D26").Interior.Color = RGB(255, 255, 0)
			S1Check = 1
	
			strr = WorkingDirectoryExport & "List_of_accounts_with_variance.xlsx"
			 
			If Stemp > 1048576 then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
			Else
			
				Call ExportDatabaseXLSX( List_of_accounts_with_variance_IDM  , "List_of_accounts_with_variance.xlsx" , "V_Report 5")
				
				Set oSheet = oBook.Worksheets.Add
				oSheet.Name = "V_Report 5"
				Set oBook2=excel.Workbooks.Open(strr)
				Set oSheet2=oBook2.Worksheets.item("V_Report 5")		
				Set oRange=oSheet2.UsedRange
							oRange.Font.Name = "Arial"
				oRange.Font.size = 10
				oRange.Copy
				oSheet.Paste 
				oBook2.Save
				oBook2.Close (True)
				Kill strr 
		
				For i = 1 To  20
					oSheet.Columns(i).EntireColumn.AutoFit
				Next i
				
				oBook.Sheets("V_Report 5").Move After:=oBook.Sheets(oBook.Sheets.Count)
			
			End If
	
		Else 
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Range("E26").value = "N/A"
		End If

	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E26").value = "N/A"
	End If

	
	
'6
	If  Fun_File_Exist(Null_TB_account_number_IDM ) Then 
			
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	sTemp = GetTotal(Null_TB_account_number_IDM  ,"" ,"DBCount" )
	oSheet.Range("D27").value = sTemp
	If sTemp > 0   Then
		
		oSheet.Range("D27").Interior.Color = RGB(255, 255, 0)
		
		strr = WorkingDirectoryExport & "Null_TB_account_number.xlsx"
		
		If Stemp > 1048576 Then
			
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
		
		Else
		
		
			Call ExportDatabaseXLSX(Null_TB_account_number_IDM , "Null_TB_account_number.xlsx" , "V_Report 6")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "V_Report 6"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("V_Report 6")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
				oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			
			oBook.Sheets("V_Report 6").Move After:=oBook.Sheets(oBook.Sheets.Count)
		End If
		
		
			Else 
			
			Set oSheet = oBook.Worksheets.Item("ValidationReport")
			oSheet.Range("E27").value = "N/A"
			
		End If 
				
		
	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E27").value = "N/A"
	End If

'7

	
	If  Fun_File_Exist(Null_TB_account_name_IDM ) Then 
		

		If ACCOUNT_DESCRIPTION_TB <> "" Then	



			Set oSheet = oBook.Worksheets.Item("ValidationReport")
			sTemp = GetTotal(Null_TB_account_name_IDM  ,"" ,"DBCount" )
			oSheet.Range("D28").value = sTemp
				If sTemp > 0   Then
					
					oSheet.Range("D28").Interior.Color = RGB(255, 255, 0)
					
					strr = WorkingDirectoryExport & "Null_TB_account_name.xlsx"
		
					
					
						Call ExportDatabaseXLSX(Null_TB_account_name_IDM, "Null_TB_account_name.xlsx" , "V_Report 7")
						
						Set oSheet = oBook.Worksheets.Add
						oSheet.Name = "V_Report 7"
						Set oBook2=excel.Workbooks.Open(strr)
						Set oSheet2=oBook2.Worksheets.item("V_Report 7")		
						Set oRange=oSheet2.UsedRange
									oRange.Font.Name = "Arial"
				oRange.Font.size = 10
						oRange.Copy
						oSheet.Paste 
						oBook2.Save
						oBook2.Close (True)
						Kill strr 
				
						For i = 1 To  20
							oSheet.Columns(i).EntireColumn.AutoFit
						Next i
						
						oBook.Sheets("V_Report 7").Move After:=oBook.Sheets(oBook.Sheets.Count)		
						
						Else 
						
					
						
						Set oSheet = oBook.Worksheets.Item("ValidationReport")
						oSheet.Range("E28").value = "N/A"
					
					
							
				End If
				
				
		Else
		
				
			oSheet.Range("D28").value = "All TB records"
			'oSheet.Range("E28").value = "Please check the whole TB"
			oSheet.Range("D28").Interior.Color = RGB(255, 255, 0)
	

			
					strr = WorkingDirectoryExport & "Null_TB_account_name.xlsx"
					
				If Stemp > 1048576 Then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
				Else
					Call ExportDatabaseXLSX(Null_TB_account_name_IDM, "Null_TB_account_name.xlsx" , "V_Report 7")
					
					Set oSheet = oBook.Worksheets.Add
					oSheet.Name = "V_Report 7"
					Set oBook2=excel.Workbooks.Open(strr)
					Set oSheet2=oBook2.Worksheets.item("V_Report 7")		
					Set oRange=oSheet2.UsedRange
					oRange.Font.Name = "Arial"
					oRange.Font.size = 10
					oRange.Copy
					oSheet.Paste 
					oBook2.Save
					oBook2.Close (True)
					Kill strr 
			
					For i = 1 To  20
						oSheet.Columns(i).EntireColumn.AutoFit
					Next i
					
					oBook.Sheets("V_Report 7").Move After:=oBook.Sheets(oBook.Sheets.Count)	
					
					
					
				End If		
		End If	
	Else 

	
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E28").value = "N/A"
	
	End If


	
'8
	If  Fun_File_Exist(Duplicate_AccountCode_TB_IDM ) Then 
			
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	sTemp = GetTotal(Duplicate_AccountCode_TB_IDM ,"" ,"DBCount" )
	oSheet.Range("D29").value = sTemp
	If sTemp > 0   Then
		
		oSheet.Range("D29").Interior.Color = RGB(255, 255, 0)
		
		strr = WorkingDirectoryExport & "Duplicate_AccountCode_TB.xlsx"
		
		If Stemp > 1048576 Then
				
				MsgBox 	"检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
				
			
		Else
			
			Call ExportDatabaseXLSX(Duplicate_AccountCode_TB_IDM, "Duplicate_AccountCode_TB.xlsx" , "V_Report 8")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "V_Report 8"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("V_Report 8")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
				oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			
			oBook.Sheets("V_Report 8").Move After:=oBook.Sheets(oBook.Sheets.Count)
		
		End If
		
			Else 
			
			Set oSheet = oBook.Worksheets.Item("ValidationReport")
			oSheet.Range("E29").value = "N/A"
			
		
				
	End If
		
	Else 
	Set oSheet = oBook.Worksheets.Item("ValidationReport")
	oSheet.Range("E29").value = "N/A"
	End If
	
	
'--------------------------↑此处为Validation Check 1-8的代码-----------------------------	



'-----------------↓此处为Validation Check 9，即完整性测试的代码-----------------------------	
'9

	If  Fun_File_Exist(Completeness_Check_IDM ) Then 

	
		Set db = Client.OpenDatabase(Completeness_Check_IDM)
		Set table = db.TableDef 
		Set task = db.TableManagement
		
		t = 0

		For i = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
			Set field = table.GetFieldAt(i) 
			If field.name = diff Then 
			t = t+1
			End If
		Next i
		
		Set field = Nothing
		Set table = Nothing	
		Set task = Nothing
		Set db = Nothing
		
		If t >0 Then
	
		Call  FieldCheck(Completeness_Check_IDM,Diff,"Numeric")
	
			Set db = Client.OpenDatabase(Completeness_Check_IDM)
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = "CompletenessTestwithDifference.IDM"
			task.AddExtraction dbName, "",  "" & DIFF & "<>0"
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)
			
		
	
			Set oSheet = oBook.Worksheets.Item("ValidationReport")
			sTemp = GetTotal(  "CompletenessTestwithDifference.IDM","" ,"DBCount" )                
		
		Call Fun_Delete_File("CompletenessTestwithDifference.IDM")

									
			
		Else 
			MsgBox "你没有手动添加差异列，请检查"
		
			sTemp = "N/A"
			
		End If
		
		
		oSheet.Range("D30").value = sTemp
		If sTemp > 0  Then 
			oSheet.Range("D30").Interior.Color = RGB(255, 255, 0)
			S1Check = 1
		End If	
	
			strr = WorkingDirectoryExport & "Completeness_Check.xlsx"
					
			Set db = Client.OpenDatabase(Completeness_Check_IDM)
			Set task = db.ExportDatabase
			
			task.AddFieldToInc "COMB_ACCOUNT_NUM"
			task.AddFieldToInc COMB_ACCOUNT_DESC
			task.AddFieldToInc Opening_Balance_TB
			task.AddFieldToInc   Dr_Amount_JE & "_SUM"
			'task.AddFieldToInc   IDEA_DEBIT_AMOUNT_JE  & "_SUM"
			task.AddFieldToInc Total_Debit_TB	
			task.AddFieldToInc   Cr_Amount_JE & "_SUM"
			'task.AddFieldToInc IDEA_Credit_AMOUNT_JE & "_SUM"
			task.AddFieldToInc Total_Credit_TB
			task.AddFieldToInc   Amount_JE & "_SUM"
			
	
			task.AddFieldToInc Ending_Balance_TB
			task.AddFieldToInc diff
			
			
			eqn = ""
			
			If runatserver = "true"	Then
			
					
				task.PerformTask  "Completeness_Check" , "V_Report 9" , "XLSX", 1, db.Count, eqn	

			
			Else
			
				task.PerformTask WorkingDirectoryExport  + "Completeness_Check.xlsx" , "V_Report 9" , "XLSX", 1, db.Count, eqn	

			End If
							
			Set db = Nothing
			Set task = Nothing
	
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "V_Report 9 Completeness_Check"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("V_Report 9")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
			oSheet.Range("A:Z").EntireColumn.AutoFit
	
			oBook.Sheets("V_Report 9 Completeness_Check").Move After:=oBook.Sheets(oBook.Sheets.Count)
			
		Set oSheet = oBook.Worksheets.Item("ValidationReport")
		oSheet.Activate
		
			oSheet.Range("A:Z").EntireColumn.AutoFit
	
	Else 
		Set oSheet = oBook.Worksheets.Item("ValidationReport")		
		oSheet.Range("D30").Interior.Color = RGB(255, 255, 0)
		oSheet.Range("D30").value = "N/A"
		oSheet.Range("E30").Interior.Color = RGB(255, 255, 0)
		oSheet.Range("E30").value = "Not performed."
		End If


	
		
'-------------------保护工作簿，保存并退出Excel----------------------------------------------
	
	PW  = Client.ManagedProject & " - " & iSplit(sFilename,"","\",1,1)
	oSheet.Protect PW, DrawingObjects:=True, Contents:=True, Scenarios:=True
		
	oBook.Save
	oBook.Close (True)
	excel.Quit
	Set oRange = Nothing
	Set oSheet = Nothing
	Set oBook = Nothing
	Set excel=Nothing
End Function


Function Step2·1_Export_AccountMapFile

Call checkAccountMappingfields

	strr= sTempExcelSource & "\AccountMapping.xlsx" 
	dstr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_AccountMapping.XLSX"
	If Len( dstr ) > 218 Then
		MsgBox "您设置的Project名称太长，将导致生成的Report错误。请在Mapping处的[sEngagement_Info]中重新设定一个较短的名称再尝试"
		End
	Else
	End If

	
	
	S1Check = 0
			
	FileCopy strr, dstr 

	Set excel = CreateObject("Excel.Application")
	
	Set oBook = excel.Workbooks.open(dstr)
	Set oSheet = oBook.Worksheets.Item("AccountMapping")
		
		Set db = Client.OpenDatabase (	AccountMapping 	)
		Set ThisTable = db.TableDef
		Set field = ThisTable.GetField ("COMB_ACCOUNT_NUM") 
			count = db.count
			Set rs = db.RecordSet
				rs.ToFirst
				For i = 1 To count
					rs.Next
					Set rec = rs.ActiveRecord
		
						If field.IsCharacter Then 
							oSheet.Cells(i+3, 1).Value = Chr(39) & rec.GetCharValue (("COMB_ACCOUNT_NUM"))
						ElseIf  field.IsNumeric Then 
							oSheet.Cells(i+3, 1).Value = rec.GetNumValue(("COMB_ACCOUNT_NUM")) 
						End If

						If  iAllTrim(rec.GetCharValue ("COMB_ACCOUNT_NUM")) <> ""  Then
							oSheet.Cells(i+3, 2).Value = rec.GetCharValue (COMB_ACCOUNT_DESC) 
						End If
				Next i
			Set rec = Nothing
		Set rs = Nothing
	Set db = Nothing
	
	oBook.save
	oBook.Close (True)
	excel.Quit
	Set oSheet = Nothing
	Set oBook = Nothing
	Set excel=Nothing
	
End Function


Function Step2·2_Upload_AccountMapping_File

	Dim obj As Object
	Dim S1, S2, S3 As String
	Dim Count, i , p As Integer 
	
	client.closeall
	
	filename = ""
	
	ChoseMappingFile :

		sFilename = "Not_OK"

		If  AccountMapping_FileforGroup = "" Then

			Set obj = Client.CommonDialogs

			filename = obj.FileOpen("",WorkingDirectoryExport & "AccountMapping.xlsx","XLSX Files (*.XLSX)|*.XLSX|All Files (*.*)|*.*||;")
		

			Set obj = Nothing	
			Set fs = CreateObject("Scripting.FileSystemObject")
		Else
		
			filename = AccountMapping_FileforGroup 
			Set fs = CreateObject("Scripting.FileSystemObject")


		End If
		
		
		If (Not fs.FileExists(filename)) Then 
			sMsg = "选择的默认文件名不存在，是否需要重新选择 ?"
			Result = MsgBox( sMsg , MB_YESNO Or MB_ICONQUESTION Or MB_DEFBUTTON1 Or MB_APPLMODAL,"Account Mapping Upload Alert!")
			If Result = IDYES Then
				GoTo ChoseMappingFile
			Else 
				Exit Function
			End If
		
		ElseIf filename = "" Then 
			sMsg = "是否取消上传AccountMapping文件?"
			Result = MsgBox( sMsg, MB_YESNO Or MB_ICONQUESTION Or MB_DEFBUTTON1 Or MB_APPLMODAL,"Cancel Upload Account Mapping file !")
			If Result = IDYES Then
				Exit Function
			Else 
				GoTo ChoseMappingFile
			End If
				
		Else
		
			
			strr= filename
		If runatserver = "true" Then
		
				
			dstr= LibraryWorkingDirectory  +"Source Files\AccountMapping.XLSX"

		Else 
				
			dstr= Client.WorkingDirectory  +"Other.ILB\AccountMapping.XLSX"

		End If 
		
			FileCopy strr, dstr 
			
	
			Dim pic As Shape
			
			Set excel = CreateObject("Excel.Application")
			Set oBook = excel.Workbooks.Open(dstr)
			
			count  = oBook.Sheets.Count
			P = 0 
			For i = 1 To count
				Set oSheet = oBook.Worksheets.Item(i)
				If oSheet.Name = "AccountMapping" Then P = 1
			Next i
						
			If P <> 1 Then
				oBook.save
				oBook.Close (True)
				excel.Quit
				Set oSheet = Nothing
				Set oBook = Nothing
				Set excel=Nothing
				sMsg = "似乎选择了错误的文件，是否要重新选择?"
				Result = MsgBox( sMsg, MB_YESNO Or MB_ICONQUESTION Or MB_DEFBUTTON1 Or MB_APPLMODAL, "Account Mapping File Alert !!!")
				If Result = IDYES Then
					GoTo ChoseMappingFile
				Else 
					Exit Function
				End If
			End If
						
			Set oSheet = oBook.Worksheets.Item("AccountMapping")
			
			
			 For Each pic In oSheet.Shapes
			 	pic.Delete		

			Next
			
			S1 = oSheet.Range("A1").value
			S2 = oSheet.Range("b1").value
			S3 = oSheet.Range("C1").value
			
			
			If S1 = "GL_Number" Or S2 = "GL_Name" Or S3 = "Standardized Account Name*" Then			'判断是否已删除表头，如已删除表头，则直接导入

			oBook.save
			oBook.Close (True)
			excel.Quit
			Set oSheet = Nothing
			Set oBook = Nothing
			Set excel=Nothing
						
			GoTo Import1
			
			End If
			 
			oSheet.Range("A1:D2").Delete 
			oSheet.Range("D:D").Delete
			
						
			S1 = oSheet.Range("A1").value
			S2 = oSheet.Range("b1").value
			S3 = oSheet.Range("C1").value
			
			oBook.save
			oBook.Close (True)
			excel.Quit
			Set oSheet = Nothing
			Set oBook = Nothing
			Set excel=Nothing
			
			

			If S1 <> "GL_Number" Or S2 <> "GL_Name" Or S3 <> "Standardized Account Name*" Then
				sMsg = "你似乎选择了错误的文件，是否要重新选择?"
				Result = MsgBox(sMsg, MB_YESNO Or MB_ICONQUESTION Or MB_DEFBUTTON1 Or MB_APPLMODAL, "Account Mapping File Alert !!!")
				If Result = IDYES Then
					GoTo ChoseMappingFile
				Else 
					Exit Function
				End If
			End If
			
		
			Import1:
			
							
			Set task = Client.GetImportTask("ImportExcel")
			dbName = dstr
			task.FileToImport = dbName
			task.SheetToImport = "AccountMapping"
			task.OutputFilePrefix = "#AccountMapping#"
			task.FirstRowIsFieldName = "TRUE"
			task.EmptyNumericFieldAsZero = "FALSE"
			task.PerformTask
			dbName = task.OutputFilePath("AccountMapping")
			Set task = Nothing
			Client.OpenDatabase(dbName)	
			
			sFilename = "OK"
		End If
		
		Client.CloseDatabase "#AccountMapping#-AccountMapping.IDM"
		Set ProjectManagement = client.ProjectManagement
		ProjectManagement.RenameDatabase "#AccountMapping#-AccountMapping.IDM", AccountMapping_File_name
		Set ProjectManagement = Nothing

		
		
End Function


Function Step3·1_Pre_Screening

Call checkPrescreeningFields  '检查必要列是否存在


'=================================================Before standard routines========================================================================
' Control Total
	Set db = Client.OpenDatabase(GL_File_name)
	db.ControlAmountField Amount_JE

' File: Join Databases把Account Mapping的科目分类Join到GL中
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.JoinDatabase
	task.FileToJoin AccountMapping_File_name
	task.IncludeAllPFields
	task.AddSFieldToInc STANDARDIZED_ACCOUNT_NAME_AM
	task.AddMatchKey ACCOUNT_NUM_JE, ACCOUNT_NUM_AM, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name03_GLwithAccountMapping
	task.PerformTask dbName, "", WI_JOIN_ALL_IN_PRIM
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

	
' Data: Direct Extraction找出关账日及以后的分录'=======================JE01=====================
	If sEnd_of_Reporting_Period_Start_Date > sPeriod_Start_Date Then
		If IS_MANUAL_JE<>""  Then
			' Data: Direct Extraction
			Set db = Client.OpenDatabase(DB_Name03_GLwithAccountMapping)
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = DB_Name04_SampleGL
			task.AddExtraction dbName, "", POSTING_DATE_JE & ">=" & Chr(34) & sEnd_of_Reporting_Period_Start_Date & Chr(34)
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)	
			
			' Data: Direct Extraction
			Set db = Client.OpenDatabase(DB_Name04_SampleGL)
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = DB_Name_JE01_01
			task.AddExtraction dbName, "", IS_MANUAL_JE &"==""1"""
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)
		
			' Data: Index Database
			Set db = Client.OpenDatabase(DB_Name_JE01_01)
			Set task = db.Index
			task.AddKey POSTING_DATE_JE, "D"
			task.AddKey DOCUMENT_NUM_JE, "D"
			task.Index FALSE
			Set task = Nothing
			Set db = Nothing			
			
			' Data: Index Database
			Set db = Client.OpenDatabase(DB_Name_JE01_01)
			Set task = db.Index
			task.AddKey POSTING_DATE_JE, "A"
			task.Index FALSE
			Set task = Nothing
			Set db = Nothing	
				
		Else
			DB_Name04_SampleGL=DB_Name_JE01_01
			' Data: Direct Extraction
			Set db = Client.OpenDatabase(DB_Name03_GLwithAccountMapping)
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = DB_Name04_SampleGL
			task.AddExtraction dbName, "", POSTING_DATE_JE & ">=" & Chr(34) & sEnd_of_Reporting_Period_Start_Date & Chr(34)
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)	
			
			' Data: Index Database
			Set db = Client.OpenDatabase(DB_Name_JE01_01)
			Set task = db.Index
			task.AddKey POSTING_DATE_JE, "D"
			task.AddKey DOCUMENT_NUM_JE, "D"
			task.Index FALSE
			Set task = Nothing
			Set db = Nothing			
		End If	
	Else 
		If IS_MANUAL_JE<>""  Then
			DB_Name04_SampleGL=DB_Name03_GLwithAccountMapping
			' Data: Direct Extraction
			Set db = Client.OpenDatabase(DB_Name04_SampleGL)
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = DB_Name_JE01_01
			task.AddExtraction dbName, "", IS_MANUAL_JE &"==""1"""
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)
		
			' Data: Index Database
			Set db = Client.OpenDatabase(DB_Name_JE01_01)
			Set task = db.Index
			task.AddKey POSTING_DATE_JE, "D"
			task.AddKey DOCUMENT_NUM_JE, "D"
			task.Index FALSE
			Set task = Nothing
			Set db = Nothing			
		
		Else
			DB_Name04_SampleGL=DB_Name03_GLwithAccountMapping
		End If
	End If
	

'=================================================JE02=======================================================================
'JE02 Direct Criteria	
	Formula_JE02=""
	For i = 0 To Len(Criteria_JE02)-Len(iremove(Criteria_JE02,Delimiter))
		Formula_JE02=Formula_JE02 & ".OR. @Isini("& Chr(34) &  isplit(Criteria_JE02,"",Delimiter,i+1) & Chr(34)& "," & DESCRIPTION_JE & ") "
	Next 
	Formula_JE02=Right(Formula_JE02,Len(Formula_JE02)-4)

' Data: Direct Extraction
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = DB_Name_JE02_01
	task.AddExtraction dbName, "",Formula_JE02
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)	
	
	
' File: Join Databases
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.JoinDatabase
	task.FileToJoin DB_Name_JE02_01
	task.IncludeAllPFields
	task.AddSFieldToInc DOCUMENT_NUM_JE
	task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name_JE02_02
	task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	
	' Data: Index Database
	Set db = Client.OpenDatabase(dbName)
	Set task = db.Index
	task.AddKey POSTING_DATE_JE, "D"
	task.AddKey DOCUMENT_NUM_JE, "D"
	task.Index FALSE
	Set task = Nothing
	Set db = Nothing
	
	
	' Remove field
	Set db = Client.OpenDatabase(dbName)
	Set task = db.TableManagement
	task.RemoveField DOCUMENT_NUM_JE & "1"
	task.PerformTask
	Set task = Nothing
	Set db = Nothing



'=================================================A_JE02=======================================================================
	If Criteria_A_JE02 <> "" Then
	'A_JE02 Direct Criteria	
		Formula_A_JE02=""
		For i = 0 To Len(Criteria_A_JE02)-Len(iremove(Criteria_A_JE02,Delimiter))
			Formula_A_JE02=Formula_A_JE02 & ".OR. @Isini("& Chr(34) &  isplit(Criteria_A_JE02,"",Delimiter,i+1) & Chr(34)& "," & DESCRIPTION_JE & ") "
		Next 
		Formula_A_JE02=Right(Formula_A_JE02,Len(Formula_A_JE02)-4)
	
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE02_01
		task.AddExtraction dbName, "",Formula_A_JE02
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)	
		
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE02_01
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE02_02
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
		
			Set db = Client.OpenDatabase(dbName)
			Set task = db.TableManagement
			task.RemoveField DOCUMENT_NUM_JE & "1"
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			
			' Data: Index Database
			Set db = Client.OpenDatabase(dbName)
			Set task = db.Index
			task.AddKey POSTING_DATE_JE, "D"
			task.AddKey DOCUMENT_NUM_JE, "D"
			task.Index FALSE
			Set task = Nothing
			Set db = Nothing


		
	End If
	
			
			


'=================================================JE03=======================================================================		
' Data: Direct Extraction
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = DB_Name_JE03_01
	task.AddExtraction dbName, "", Amount_JE & "<0 .AND." & STANDARDIZED_ACCOUNT_NAME_AM & " ==""Revenue"""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)


' File: Join Databases
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.JoinDatabase
	task.FileToJoin DB_Name_JE03_01
	task.IncludeAllPFields
	task.AddSFieldToInc DOCUMENT_NUM_JE
	task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name_JE03_02
	task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)


' Data: Direct Extraction
	Set db = Client.OpenDatabase(DB_Name_JE03_02)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = DB_Name_JE03_03
	task.AddExtraction dbName, "", Amount_JE & ">0 .AND." & STANDARDIZED_ACCOUNT_NAME_AM & " <>""Cash or Cash at Bank"" .AND. " & STANDARDIZED_ACCOUNT_NAME_AM & " <>""Trade Receivables"" .AND. " & STANDARDIZED_ACCOUNT_NAME_AM & " <>""Receipt in advance"" .AND. "  & STANDARDIZED_ACCOUNT_NAME_AM & " <>""Revenue""" 
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)


' File: Join Databases
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.JoinDatabase
	task.FileToJoin DB_Name_JE03_03
	task.IncludeAllPFields
	task.AddSFieldToInc DOCUMENT_NUM_JE
	task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name_JE03_04
	task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	

	' Data: Index Database
	Set db = Client.OpenDatabase(dbName)
	Set task = db.Index
	task.AddKey POSTING_DATE_JE, "D"
	task.AddKey DOCUMENT_NUM_JE, "D"
	task.Index FALSE
	Set task = Nothing
	Set db = Nothing
	
	'Remove field
	
	Set db = Client.OpenDatabase(dbName)
	Set task = db.TableManagement
	task.RemoveField DOCUMENT_NUM_JE & "1"
	task.PerformTask
	Set task = Nothing
	Set db = Nothing

	
'=================================================A_JE03_1=======================================================================
	If Criteria_A_JE03_1 <> "" Then
	'A_JE03_1 Direct Criteria
		Criteria_A_JE03_1_DR=isplit(Criteria_A_JE03_1,"",Delimiter,1)
		Criteria_A_JE03_1_CR=isplit(Criteria_A_JE03_1,"",Delimiter,2)
		Formula_A_JE03_1=""
		For i = 0 To Len(Criteria_A_JE03_1_CR)-Len(iremove(Criteria_A_JE03_1_CR,","))
			Formula_A_JE03_1=Formula_A_JE03_1 & ".OR. " & STANDARDIZED_ACCOUNT_NAME_AM & "==" & Chr(34) & isplit(Criteria_A_JE03_1_CR,"",",",i+1) & Chr(34)
		Next 
		Formula_A_JE03_1=Amount_JE & "<0 .AND. (" & Right(Formula_A_JE03_1,Len(Formula_A_JE03_1)-4) & ")"
		
		
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE03_1_01
		task.AddExtraction dbName, "", Formula_A_JE03_1
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE03_1_01
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE03_1_02
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	'A_JE03_1 Direct Criteria
		Formula_A_JE03_1=""
		For i = 0 To Len(Criteria_A_JE03_1_DR)-Len(iremove(Criteria_A_JE03_1_DR,","))
			Formula_A_JE03_1=Formula_A_JE03_1 & ".OR. " & STANDARDIZED_ACCOUNT_NAME_AM & "==" & Chr(34) & isplit(Criteria_A_JE03_1_DR,"",",",i+1) & Chr(34)
		Next 
		Formula_A_JE03_1=Amount_JE & ">0 .AND. (" & Right(Formula_A_JE03_1,Len(Formula_A_JE03_1)-4) & ")"
	
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name_A_JE03_1_02)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE03_1_03
		task.AddExtraction dbName, "", Formula_A_JE03_1
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE03_1_03
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE03_1_04
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
		
			' Data: Index Database
	Set db = Client.OpenDatabase(dbName)
	Set task = db.Index
	task.AddKey POSTING_DATE_JE, "D"
	task.AddKey DOCUMENT_NUM_JE, "D"
	task.Index FALSE
	Set task = Nothing
	Set db = Nothing
	
	'Remove field
	
	Set db = Client.OpenDatabase(dbName)
	Set task = db.TableManagement
	task.RemoveField DOCUMENT_NUM_JE & "1"
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
		
	End If
	
	


	
		
'=================================================A_JE03_2=======================================================================
	If Criteria_A_JE03_2 <>"" Then
	'A_JE03_2 Direct Criteria
		Criteria_A_JE03_2_DR=isplit(Criteria_A_JE03_2,"",Delimiter,1)
		Criteria_A_JE03_2_CR=isplit(Criteria_A_JE03_2,"",Delimiter,2)
		Formula_A_JE03_2=""
		For i = 0 To Len(Criteria_A_JE03_2_CR)-Len(iremove(Criteria_A_JE03_2_CR,","))
			Formula_A_JE03_2=Formula_A_JE03_2 & ".OR. " & STANDARDIZED_ACCOUNT_NAME_AM & "==" & Chr(34) & isplit(Criteria_A_JE03_2_CR,"",",",i+1) & Chr(34)
		Next 
		Formula_A_JE03_2=Amount_JE & "<0 .AND. (" & Right(Formula_A_JE03_2,Len(Formula_A_JE03_2)-4) & ")"
		
		
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE03_2_01
		task.AddExtraction dbName, "", Formula_A_JE03_2
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE03_2_01
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE03_2_02
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	'A_JE03_2 Direct Criteria
		Formula_A_JE03_2=""
		For i = 0 To Len(Criteria_A_JE03_2_DR)-Len(iremove(Criteria_A_JE03_2_DR,","))
			Formula_A_JE03_2=Formula_A_JE03_2 & ".OR. " & STANDARDIZED_ACCOUNT_NAME_AM & "==" & Chr(34) & isplit(Criteria_A_JE03_2_DR,"",",",i+1) & Chr(34)
		Next 
		Formula_A_JE03_2=Amount_JE & ">0 .AND. (" & Right(Formula_A_JE03_2,Len(Formula_A_JE03_2)-4) & ")"
	
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name_A_JE03_2_02)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE03_2_03
		task.AddExtraction dbName, "", Formula_A_JE03_2
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE03_2_03
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE03_2_04
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
		
		' Data: Index Database
		Set db = Client.OpenDatabase(dbName)
		Set task = db.Index
		task.AddKey POSTING_DATE_JE, "D"
		task.AddKey DOCUMENT_NUM_JE, "D"
		task.Index FALSE
		Set task = Nothing
		Set db = Nothing
	
		'Remove field
		
		Set db = Client.OpenDatabase(dbName)
		Set task = db.TableManagement
		task.RemoveField DOCUMENT_NUM_JE & "1"
		task.PerformTask
		Set task = Nothing
		Set db = Nothing
		
		
	End If

	


'=================================================JE04=======================================================================
' Append Field
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.TableManagement
	Set field = db.TableDef.NewField
	Set stats = db.FieldStats(IDEA_DEBIT_AMOUNT_JE )	'一定要先运行Field Statistics
	field.Name = JE04_ENDING_NUM_JE
	field.Description = "Identify the rounded amount according to the average of total debit amount"
	field.Type = WI_VIRT_CHAR
	field.Equation = " @Repeat(""0"", @Len(@Str(@Int(@FieldStatistics(" & chr(34)  & IDEA_DEBIT_AMOUNT_JE & chr(34)&", 11)), 1, 0))-1)"
	field.Length = 1000
	task.AppendField field
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Set field = Nothing

' Data: Direct Extraction
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = DB_Name_JE04_01
	task.AddExtraction dbName, "", " @Right(@Str(@Int( " & Amount_JE & "), 1, 0), @Len(" & JE04_ENDING_NUM_JE & ")) == " & JE04_ENDING_NUM_JE
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

' File: Join Databases
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.JoinDatabase
	task.FileToJoin DB_Name_JE04_01
	task.IncludeAllPFields
	task.AddSFieldToInc DOCUMENT_NUM_JE
	task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
	task.CreateVirtualDatabase = False
	dbName = DB_Name_JE04_02
	task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	

	' Data: Index Database
	Set db = Client.OpenDatabase(dbName)
	Set task = db.Index
	task.AddKey POSTING_DATE_JE, "D"
	task.AddKey DOCUMENT_NUM_JE, "D"
	task.Index FALSE
	Set task = Nothing
	Set db = Nothing
	'Remove Field
	Set db = Client.OpenDatabase(dbName)
	Set task = db.TableManagement
	task.RemoveField DOCUMENT_NUM_JE & "1"
	task.PerformTask
	Set task = Nothing
	Set db = Nothing	

'=================================================A_JE04=======================================================================
	If Criteria_A_JE04 <>"" Then
	'A_JE04 Direct Criteria	
		Formula_A_JE04=""
		For i = 0 To Len(Criteria_A_JE04)-Len(iremove(Criteria_A_JE04,Delimiter))
			Formula_A_JE04=Formula_A_JE04 & ".OR. (@Right(@Str(@Int( " & Amount_JE & "), 1, 0), @Len(" & Chr(34) & isplit(Criteria_A_JE04,"",Delimiter,i+1) & Chr(34) & ")) == " & Chr(34) &  isplit(Criteria_A_JE04,"",Delimiter,i+1) & Chr(34) &")"
		Next 
		Formula_A_JE04=Right(Formula_A_JE04,Len(Formula_A_JE04)-4)
	
	' Data: Direct Extraction
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.Extraction
		task.IncludeAllFields
		dbName = DB_Name_A_JE04_01
		task.AddExtraction dbName, "", Formula_A_JE04
		task.CreateVirtualDatabase = False
		task.PerformTask 1, db.Count
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	' File: Join Databases
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.JoinDatabase
		task.FileToJoin DB_Name_A_JE04_01
		task.IncludeAllPFields
		task.AddSFieldToInc DOCUMENT_NUM_JE
		task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
		task.CreateVirtualDatabase = False
		dbName = DB_Name_A_JE04_02
		task.PerformTask dbName, "", WI_JOIN_MATCH_ONLY
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
		
			
		' Data: Index Database
		Set db = Client.OpenDatabase(dbName)
		Set task = db.Index
		task.AddKey POSTING_DATE_JE, "D"
		task.AddKey DOCUMENT_NUM_JE, "D"
		task.Index FALSE
		Set task = Nothing
		Set db = Nothing
		'Remove Field
		Set db = Client.OpenDatabase(dbName)
		Set task = db.TableManagement
		task.RemoveField DOCUMENT_NUM_JE & "1"
		task.PerformTask
		Set task = Nothing
		Set db = Nothing		
		
	End If
	

	
	
'=================================================JE05=======================================================================
	If UESR_JE<>"" Then
	' Analysis: Summarization
		Set db = Client.OpenDatabase(DB_Name04_SampleGL)
		Set task = db.Summarization
		task.AddFieldToSummarize UESR_JE
		task.AddFieldToTotal Amount_JE
		task.AddFieldToTotal IDEA_DEBIT_AMOUNT_JE
		task.AddFieldToTotal IDEA_CREDIT_AMOUNT_JE
		dbName = DB_Name_JE05_01
		task.OutputDBName = dbName
		task.CreatePercentField = FALSE
		task.StatisticsToInclude = SM_SUM
		task.PerformTask
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	
	' Data: Sort
		Set db = Client.OpenDatabase(DB_Name_JE05_01)
		Set task = db.Sort
		task.AddKey "NO_OF_RECS", "A"
		dbName = DB_Name_JE05_02
		task.PerformTask dbName
		Set task = Nothing
		Set db = Nothing
		Client.OpenDatabase (dbName)
	End If

'=================================================JE06=======================================================================
' Analysis: Summarization
	Set db = Client.OpenDatabase(DB_Name04_SampleGL)
	Set task = db.Summarization
	task.AddFieldToSummarize ACCOUNT_NUM_JE
	task.AddFieldToInc ACCOUNT_DESCRIPTION_JE
	task.AddFieldToTotal Amount_JE
	task.AddFieldToTotal IDEA_DEBIT_AMOUNT_JE
	task.AddFieldToTotal IDEA_CREDIT_AMOUNT_JE
	dbName = DB_Name_JE06_01
	task.OutputDBName = dbName
	task.CreatePercentField = FALSE
	task.UseFieldFromFirstOccurrence = TRUE
	task.StatisticsToInclude = SM_SUM
	task.PerformTask
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

' Data: Sort
	Set db = Client.OpenDatabase(DB_Name_JE06_01)
	Set task = db.Sort
	task.AddKey "NO_OF_RECS", "A"
	dbName = DB_Name_JE06_02
	task.PerformTask dbName
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)

	client.closeall
End Function


Function STep3·3_Export_Power_BI
'=================================================For Power BI=======================================================================
Dim Arr(7,5)As String
'将数据录入数组
'0录入(完整)分录的Database name
Arr(0,0)=DB_Name_JE01_01
Arr(1,0)=DB_Name_JE02_02
Arr(2,0)=DB_Name_A_JE02_02
Arr(3,0)=DB_Name_JE03_04
Arr(4,0)=DB_Name_A_JE03_1_04
Arr(5,0)=DB_Name_A_JE03_2_04
Arr(6,0)=DB_Name_JE04_02
Arr(7,0)=DB_Name_A_JE04_02

'1录入判断列的Field name
Arr(0,1)=PRESCR_R1
Arr(1,1)=PRESCR_R2
Arr(2,1)=PRESCR_A2
Arr(3,1)=PRESCR_R3
Arr(4,1)=PRESCR_A3_1
Arr(5,1)=PRESCR_A3_2
Arr(6,1)=PRESCR_R4
Arr(7,1)=PRESCR_A4

'2录入判断列的Description
Arr(0,2)="Identify manual journal entries"
Arr(1,2)="Identify journal entries with specific comments"
Arr(2,2)=Arr(1,2)
Arr(3,2)="Identify potential unexpected journal entries"
Arr(4,2)=Arr(3,2)
Arr(5,2)=Arr(3,2)
Arr(6,2)="Identify journal entries with rounded amounts"
Arr(7,2)=Arr(6,2)

'3录入暂存Power BI信息的Database name
Arr(0,3)=DB_NAME_BI_01
Arr(1,3)=DB_NAME_BI_02
Arr(2,3)=DB_NAME_BI_03
Arr(3,3)=DB_NAME_BI_04
Arr(4,3)=DB_NAME_BI_05
Arr(5,3)=DB_NAME_BI_06
Arr(6,3)=DB_NAME_BI_07
Arr(7,3)=DB_NAME_BI_FinalPopulation

'4录入每个routine的criteria
Arr(0,4)=IS_MANUAL_JE
Arr(1,4)=Criteria_JE02
Arr(2,4)=Criteria_A_JE02
Arr(3,4)="Criteria_JE03"	'这个数据录入没有意义，只是为了让Criteria非空，从而执行下面的循环
Arr(4,4)=Criteria_A_JE03_1
Arr(5,4)=Criteria_A_JE03_2
Arr(6,4)="Criteria_JE04"	'这个数据录入没有意义，只是为了让Criteria非空，从而执行下面的循环
Arr(7,4)=Criteria_A_JE04


' File: Append Field & Join Databases	


'必须保证DB_NAME_BI_01会被创造出来，因为之后会有删除database的步骤
	If sEnd_of_Reporting_Period_Start_Date > sPeriod_Start_Date  Then
		If IS_MANUAL_JE<>""  Then
			If Client.OpenDatabase(Arr(0,0)).count>0 Then	'0(完整)分录的Database name
				'Remove Field删除IDEA Script自动增加的列
				Set db = Client.OpenDatabase(Arr(0,0))	'0(完整)分录的Database name
				Set table = db.TableDef 
				Set task = db.TableManagement
				For j = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
					Set field = table.GetFieldAt(j) 
					If field.name =Arr(0,1)Then	'1判断列的Field name
						task.RemoveField field.name
						task.PerformTask	
					End If
				Next j	
				Set field = Nothing
				Set table = Nothing	
				Set task = Nothing
		
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(0,1)	'1判断列的Field name
				field.Description = Arr(0,2)	'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """Y"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing
				
				Set db = Client.OpenDatabase(DB_Name04_SampleGL)	
				Set task = db.JoinDatabase
				task.FileToJoin Arr(0,0)	'0(完整)分录的Database name
				task.IncludeAllPFields
				task.AddSFieldToInc Arr(0,1)	'1判断列的Field name
				task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
				task.CreateVirtualDatabase = False
				dbName = Arr(0,3)		'3暂存Power BI信息的Database name
				task.PerformTask dbName, "", WI_JOIN_ALL_IN_PRIM
				Set task = Nothing
				Set db = Nothing
				Client.OpenDatabase (dbName)			
			Else
				Set db = Client.OpenDatabase(DB_Name04_SampleGL)
				Set task = db.Extraction
				task.IncludeAllFields
				dbName = Arr(0,3)		'3暂存Power BI信息的Database name
				task.AddExtraction dbName, "", ""
				task.CreateVirtualDatabase = False
				task.PerformTask 1, db.Count
				Set task = Nothing
				Set db = Nothing
				Client.OpenDatabase (dbName)
		
				Set db = Client.OpenDatabase(Arr(0,3))		'3暂存Power BI信息的Database name
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(0,1)	'1判断列的Field name
				field.Description = Arr(0,2)	'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing			
			End If			
		Else		
			'Remove Field删除IDEA Script自动增加的列
			Set db = Client.OpenDatabase(Arr(0,0))	'0(完整)分录的Database name
			Set table = db.TableDef 
			Set task = db.TableManagement
			For j = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
				Set field = table.GetFieldAt(j) 
				If field.name =Arr(0,1)Then	'1判断列的Field name
					task.RemoveField field.name
					task.PerformTask	
				End If
			Next j	
			Set field = Nothing
			Set table = Nothing	
			Set task = Nothing
						
			Set db = Client.OpenDatabase(Arr(0,0))		'0(完整)分录的Database name
			Set task = db.TableManagement
			Set field = db.TableDef.NewField
			field.Name = Arr(0,1)	'1判断列的Field name
			field.Description = Arr(0,2)	'2判断列的Description
			field.Type = WI_VIRT_CHAR
			field.Equation = """Y"""
			field.Length = 1
			task.AppendField field
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			Set field = Nothing				
			
			Set db = Client.OpenDatabase(Arr(0,0)) '0(完整)分录的Database name
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = Arr(0,3)		'3暂存Power BI信息的Database name
			task.AddExtraction dbName, "", ""
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)
		End If
	Else	'当sEnd_of_Reporting_Period_Start_Date <= sPeriod_Start_Date 时
		If IS_MANUAL_JE<>""  Then
			If Client.OpenDatabase(Arr(0,0)).count>0 Then	'0(完整)分录的Database name
				'Remove Field删除IDEA Script自动增加的列
				Set db = Client.OpenDatabase(Arr(0,0))	'0(完整)分录的Database name
				Set table = db.TableDef 
				Set task = db.TableManagement
				For j = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
					Set field = table.GetFieldAt(j) 
					If field.name =Arr(0,1)Then	'1判断列的Field name
						task.RemoveField field.name
						task.PerformTask	
					End If
				Next j	
				Set field = Nothing
				Set table = Nothing	
				Set task = Nothing
		
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(0,1)	'1判断列的Field name
				field.Description = Arr(0,2)	'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """Y"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing
				
				Set db = Client.OpenDatabase(DB_Name03_GLwithAccountMapping)	
				Set task = db.JoinDatabase
				task.FileToJoin Arr(0,0)	'0(完整)分录的Database name
				task.IncludeAllPFields
				task.AddSFieldToInc Arr(0,1)	'1判断列的Field name
				task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
				task.CreateVirtualDatabase = False
				dbName = Arr(0,3)		'3暂存Power BI信息的Database name
				task.PerformTask dbName, "", WI_JOIN_ALL_IN_PRIM
				Set task = Nothing
				Set db = Nothing
				Client.OpenDatabase (dbName)			
			Else				
				Set db = Client.OpenDatabase(DB_Name03_GLwithAccountMapping) 
				Set task = db.Extraction
				task.IncludeAllFields
				dbName = Arr(0,3)		'3暂存Power BI信息的Database name
				task.AddExtraction dbName, "", ""
				task.CreateVirtualDatabase = False
				task.PerformTask 1, db.Count
				Set task = Nothing
				Set db = Nothing
				Client.OpenDatabase (dbName)	
				
				Set db = Client.OpenDatabase(Arr(0,3))		'3暂存Power BI信息的Database name
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(0,1)	'1判断列的Field name
				field.Description = Arr(0,2)	'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing							
			End If						
		Else
			Set db = Client.OpenDatabase(DB_Name03_GLwithAccountMapping) 
			Set task = db.Extraction
			task.IncludeAllFields
			dbName = Arr(0,3)		'3暂存Power BI信息的Database name
			task.AddExtraction dbName, "", ""
			task.CreateVirtualDatabase = False
			task.PerformTask 1, db.Count
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)	
			
			Set db = Client.OpenDatabase(Arr(0,3))		'3暂存Power BI信息的Database name
			Set task = db.TableManagement
			Set field = db.TableDef.NewField
			field.Name = Arr(0,1)	'1判断列的Field name
			field.Description = Arr(0,2)	'2判断列的Description
			field.Type = WI_VIRT_CHAR
			field.Equation = """Y"""
			field.Length = 1
			task.AppendField field
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			Set field = Nothing				
		End If
	End If

	
	
	For i = LBound(Arr,1)+1 To UBound(arr,1)
		'判断找出的（完整）分录是否有结果
		If Arr(i,4)<>""  Then
			If  Client.OpenDatabase(Arr(i,0)).count>0 Then	'4每个routine的criteria'0(完整)分录的Database name
	
				'Remove Field删除IDEA Script自动增加的列
				Set db = Client.OpenDatabase(Arr(i,0))	'0(完整)分录的Database name
				Set table = db.TableDef 
				Set task = db.TableManagement
				For j = table.count To 1 Step -1		'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
					Set field = table.GetFieldAt(j) 
					If field.name =Arr(i,1) Then		'1判断列的Field name
						task.RemoveField field.name
						task.PerformTask	
					End If
				Next j	
				Set field = Nothing
				Set table = Nothing	
				Set task = Nothing
				
				'新增判断列
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(i,1)	'1判断列的Field name
				field.Description = Arr(i,2)	'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """Y"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing
				
				Set db = Client.OpenDatabase(Arr(i-1,3)) 	'上一个3暂存Power BI信息的Database name
				Set task = db.JoinDatabase
				task.FileToJoin Arr(i,0)	'0(完整)分录的Database name
				task.IncludeAllPFields
				task.AddSFieldToInc Arr(i,1)	'1判断列的Field name
				task.AddMatchKey DOCUMENT_NUM_JE,DOCUMENT_NUM_JE, "A"
				task.CreateVirtualDatabase = False
				dbName = Arr(i,3)		'3暂存Power BI信息的Database name
				task.PerformTask dbName, "", WI_JOIN_ALL_IN_PRIM
				Set task = Nothing
				Set db = Nothing
				Client.OpenDatabase (dbName)	
				client.closeall	'关闭所有database后才删除database
				Client.DeleteDatabase	Arr(i-1,3)	'删除上一个3暂存Power BI信息的Database name
			Else
				Arr(i,3)=Arr(i-1,3)	'3暂存Power BI信息的Database name'这里进行一个赋值是因为循环会用到上一个Power BI的Database
				Set db = Client.OpenDatabase(Arr(i,3))	'3暂存Power BI信息的Database name
				Set task = db.TableManagement
				Set field = db.TableDef.NewField
				field.Name = Arr(i,1)	'1判断列的Field name
				field.Description = Arr(i,2)		'2判断列的Description
				field.Type = WI_VIRT_CHAR
				field.Equation = """"""
				field.Length = 1
				task.AppendField field
				task.PerformTask
				Set task = Nothing
				Set db = Nothing
				Set field = Nothing	
				
				'当DB_NAME_BI_FinalPopulation不会被Join出来的时候，最后要重命名DB_NAME_BI_07
				
				If  i= UBound(arr,1) Then
					client.closeall'关闭所有database后才重命名database
					Set ProjectManagement = client.ProjectManagement
					ProjectManagement.RenameDatabase Arr(i,3), DB_NAME_BI_FinalPopulation
					Set ProjectManagement = Nothing		
				End If
			End If
		Else
			Arr(i,3)=Arr(i-1,3)	'3暂存Power BI信息的Database name'这里进行一个赋值是因为循环会用到上一个Power BI的Database
			Set db = Client.OpenDatabase(Arr(i,3))	'3暂存Power BI信息的Database name
			Set task = db.TableManagement
			Set field = db.TableDef.NewField
			field.Name = Arr(i,1)	'1判断列的Field name
			field.Description = Arr(i,2)		'2判断列的Description
			field.Type = WI_VIRT_CHAR
			field.Equation = """"""
			field.Length = 1
			task.AppendField field
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			Set field = Nothing	
			
			'当DB_NAME_BI_FinalPopulation不会被Join出来的时候，最后要重命名DB_NAME_BI_07
			If  i= UBound(arr,1) Then
				client.closeall'关闭所有database后才重命名database
				Set ProjectManagement = client.ProjectManagement
				ProjectManagement.RenameDatabase Arr(i,3), DB_NAME_BI_FinalPopulation
				Set ProjectManagement = Nothing		
			End If					
		End If	
	Next i
	
	Set db = Client.OpenDatabase(GL_File_name)
	Set task = db.Extraction
	task.IncludeAllFields
	dbName = DB_NAME_BI_FinalPeriod
	task.AddExtraction dbName, "", ""
	task.CreateVirtualDatabase = False
	task.PerformTask 1, db.Count
	Set task = Nothing
	Set db = Nothing
	Client.OpenDatabase (dbName)
	Erase Arr

	
'========================================Standardize Power BI Field Info===============================================
	Dim Crr(1)As String
	Crr(0)=DB_NAME_BI_FinalPopulation
	Crr(1)=DB_NAME_BI_FinalPeriod

	Dim Brr(12) As String
	Brr(0)=ACCOUNT_NUM_JE
	Brr(1)=FIELD_NAME_BI_ACCOUNT_NUM_JE
	Brr(2)=ACCOUNT_DESCRIPTION_JE
	Brr(3)=FIELD_NAME_BI_ACCOUNT_DESC_JE
	Brr(4)=DOCUMENT_NUM_JE
	Brr(5)=FIELD_NAME_BI_DOCUMENT_NUM_JE
	Brr(6)=DESCRIPTION_JE
	Brr(7)=FIELD_NAME_BI_DESC_JE
	Brr(8)=UESR_JE
	Brr(9)=FIELD_NAME_BI_CREATE_BY_JE
	Brr(10)=IS_MANUAL_JE
	Brr(11)=FIELD_NAME_BI_MANUAL_JE
		
	For i=LBound(Crr) To UBound(Crr)
		
		'=============================Modify Field Name================================
		'先modify，再append，不然会影响公式对field name的引用

		
		
		For j=LBound(Brr) To UBound(Brr) Step 2
			If Brr(j)<>"" Then
			
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement

				'修改Character Field	
				Set field = db.TableDef.NewField
				field.Name = Brr(j+1)
				field.Type = WI_CHAR_FIELD
				field.Length = 1024
				task.ReplaceField Brr(j), field
				task.PerformTask
				Set db = Nothing
				Set task = Nothing			
				Set field = Nothing
			End If
		Next j		



		
		'修改Numeric Field
		
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_AMOUNT_JE
		field.Type = WI_NUM_FIELD
		field.Decimals = 2
		task.ReplaceField Amount_JE, field
		task.PerformTask
		Set db = Nothing
		Set task = Nothing
		Set field = Nothing
		
		'修改Date Field
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_POSTING_DATE_JE
		field.Type = WI_DATE_FIELD
		field.Equation = "YYYYMMDD"
		task.ReplaceField POSTING_DATE_JE , field
		task.PerformTask
		Set db = Nothing
		Set task = Nothing
		Set field = Nothing
								
		'====================Append Info Field============================
		If UESR_JE="" Then
		
			Set db = Client.OpenDatabase(Crr(i))
			Set task = db.TableManagement
			Set field = db.TableDef.NewField
			field.Name = FIELD_NAME_BI_CREATE_BY_JE
			field.Description = "Blank user in GL"
			field.Type = WI_VIRT_CHAR
			field.Equation = """"""
			field.Length = 1
			task.AppendField field
			task.PerformTask
			Set field = Nothing				
			Set db = Nothing
			Set task = Nothing
		
		End If

		If IS_MANUAL_JE="" Then
		
			Set db = Client.OpenDatabase(Crr(i))
			Set task = db.TableManagement
			Set field = db.TableDef.NewField
			field.Name = FIELD_NAME_BI_MANUAL_JE
			field.Description = "Manual journal entries in GL"
			field.Type = WI_VIRT_CHAR
			field.Equation = """1"""
			field.Length = 1
			task.AppendField field
			task.PerformTask
			Set field = Nothing
			Set db = Nothing
			Set task = Nothing
		End If		
		


		
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_Enagement_Info
		field.Description = "Information of Engagement"
		field.Type = WI_VIRT_CHAR	'WI_CHAR_FIELD
		field.Equation = Chr(34) & sEngagement_Info & Chr(34)
		field.Length = 1024
		task.AppendField field
		task.PerformTask
		Set field = Nothing
		Set db = Nothing
		Set task = Nothing
			
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_Period_Start
		field.Description = "Period start date"
		field.Type =WI_VIRT_DATE	'WI_DATE_FIELD
		field.Equation = "@Ctod("& Chr(34) & sPeriod_Start_Date & Chr(34) &",""YYYYMMDD"")"
		task.AppendField field
		task.PerformTask
		Set field = Nothing
		Set db = Nothing
		Set task = Nothing
		
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement		
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_Period_End
		field.Description = "Period end date"
		field.Type = WI_VIRT_DATE	'WI_DATE_FIELD
		field.Equation = "@Ctod("& Chr(34) & sPeriod_End_Date & Chr(34) &",""YYYYMMDD"")"
		task.AppendField field
		task.PerformTask
		Set field = Nothing
		Set db = Nothing
		Set task = Nothing
		
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_Period_Lastdate
		field.Description = "The start date of the end of the reporting period"
		field.Type = WI_VIRT_DATE	'WI_DATE_FIELD
		field.Equation = "@Ctod("& Chr(34) & sEnd_of_Reporting_Period_Start_Date & Chr(34) &",""YYYYMMDD"")"
		task.AppendField field
		task.PerformTask
		Set field = Nothing
		Set db = Nothing
		Set task = Nothing				
				
		Set db = Client.OpenDatabase(Crr(i))
		Set task = db.TableManagement
		Set field = db.TableDef.NewField
		field.Name = FIELD_NAME_BI_Population
		field.Description = "Identify the population journal entries"
		field.Type = WI_VIRT_CHAR	'WI_CHAR_FIELD
		field.Equation = "@If( " & FIELD_NAME_BI_POSTING_DATE_JE & ">=" & Chr(34) & sEnd_of_Reporting_Period_Start_Date & Chr(34) &", ""1"", ""0"")"
		field.Length = 1
		task.AppendField field
		task.PerformTask
		Set field = Nothing		
		Set db = Nothing
		Set task = Nothing	
									
	Next i	
	Erase Crr
	Erase Brr		

		If  Fun_File_Exist(  DB_NAME_BI_FinalPeriod  ) Then 
			sTemp = GetTotal(DB_NAME_BI_FinalPeriod ,"" ,"DBCount" )
			
			If sTemp < 1048576   Then
				Call OpenPowerBIfromExcel
			Else
				Call OpenPowerBIfromIDEADB
			End If
			
		End If		
End Function




Function OpenPowerBIfromExcel


		strr=WorkingDirectoryExport & "#GL#In_Period.xlsx"
		If Dir(strr)<>"" Then
		Kill strr 
		End If 

		strr=WorkingDirectoryExport & "#GL#_Population.xlsx"
		If Dir(strr)<>"" Then
		Kill strr 
		End If 

		Call ExportDatabaseXLSX( DB_NAME_BI_FinalPeriod  , "#GL#In_Period.xlsx" , "database")
		Call ExportDatabaseXLSX( DB_NAME_BI_FinalPopulation  , "#GL#_Population.xlsx" , "database")
		
		
		strr=WorkingDirectoryExport & "#GL#In_Period.xlsx"
		 dstr = sTempExcelSource & "\#GL#In_Period.xlsx"
		
		Set db=Client.OpenDatabase("#GL#_Population.IDM")
		
		
		
		If db.count >0 Then '如果GL存在，那么才继续做后续的操作。
		
			
	
			If Dir(dstr)<>"" Then
			Kill dstr 
			End If 
			FileCopy strr,dstr 
			
			 strr=WorkingDirectoryExport & "#GL#_Population.xlsx"
			 dstr = sTempExcelSource & "\#GL#_Population.xlsx"
			 
				If Dir(strr)<>"" Then
					Set db=Client.OpenDatabase("#GL#_Population.IDM")
					If db.count<>0 Then
					Else
				 	 'strr=Client.WorkingDirectory & "\#GL#BI_ Final_Databases.IDM"
					 End If
				End If 
			If Dir(dstr)<>"" Then
			Kill dstr 
			End If 
			FileCopy strr,dstr 
			
			'strr=WorkingDirectoryExport & "\Pre_screening.pbix"
			
			strr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_Pre-screeningReport.pbix"
			
		 	dstr = sTempExcelSource & "\Pre_screening.pbix"
		
			FileCopy dstr ,strr
			
			
			 Dim sstemp As String
			 
			 Shell "C:\Program Files\Microsoft Power BI Desktop RS\bin\PBIDesktop.exe " & Chr(34) & strr & Chr(34)
		 Else 
		 
			 MsgBox "没有找到对应的数据源，请检查是否执行了PreScreening及是否有结果！"
		 
		 
		End If 
		
		
End Function

Function OpenPowerBIfromIDEADB

		strr=Client.WorkingDirectory & "\#GL#In_Period.IDM"
		 dstr =sTempExcelSource & "\#GL#In_Period.IDM"
		
		Set db=Client.OpenDatabase("#GL#_Population.IDM")
		
		
		
		If db.count >0 Then '如果GL存在，那么才继续做后续的操作。
		
		

		If Dir(dstr)<>"" Then
		Kill dstr 
		End If 
		FileCopy strr,dstr 
		
		 strr=Client.WorkingDirectory & "\#GL#_Population.IDM"
		 dstr = sTempExcelSource &"\#GL#_Population.IDM"
			If Dir(strr)<>"" Then
				Set db=Client.OpenDatabase("#GL#_Population.IDM")
				If db.count<>0 Then
				Else
			 	 strr=Client.WorkingDirectory & "\#GL#BI_ Final_Databases.IDM"
				 End If
			End If 
		If Dir(dstr)<>"" Then
		Kill dstr 
		End If 
		FileCopy strr,dstr 
		
	strr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_Pre-screeningReport.pbix"
	
 	dstr = sTempExcelSource & "\Pre_screeningforIDEA.pbix"

	FileCopy dstr ,strr
		
		
		 Dim sstemp As String
		 
		 Shell "C:\Program Files\Microsoft Power BI Desktop RS\bin\PBIDesktop.exe " & Chr(34) & strr & Chr(34)
		 Else 
		 
		 MsgBox "没有找到对应的数据源，请检查是否执行了PreScreening及是否有结果！"
		 
		 
		End If 
End Function



Function Step3·2_Export_Prescreening

	Dim sTemp As String
	Dim db  As database
	Dim rs As recordset
	Dim ThisTable As Object
	Dim field As field
	Dim rec As Object
	Dim i As Long
	Dim j As Integer
	Dim iFieldCount As Integer
	Dim P As Integer
	
	Dim objUser As String 
	Set WSHnet = CreateObject("WScript.Network")
	Let UserName = WSHnet.UserName
	Let UserDomain = WSHnet.UserDomain
	On Error Resume Next	
	Set objUser = GetObject("WinNT://" & UserDomain & "/" & UserName & ",user")
	Let UserFullName = objUser.FullName
	
	If UserFullName = "" Then UserFullName = UserName 
	
	'sProjectFolder = client.WorkingDirectory
	
	strr= sTempExcelSource & "\Pre-screeningReport-KDC.xlsx"   ' "Exports.ILB\Pre-screeningReport-KDC.xlsx"
	dstr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_Pre-screeningReport-KDC.xlsx"

	If Len( dstr ) > 218 Then
		MsgBox "您设置的Project名称太长，将导致生成的Report错误。请在Mapping处的[sEngagement_Info]中重新设定一个较短的名称再尝试"
		End
	Else
	End If
		
	FileCopy strr, dstr

	
	Set excel = CreateObject("Excel.Application")
	Set oBook = excel.Workbooks.Open(dstr)
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")


'---------------------------------------------------------------------------项目基本信息取值-----------------------------------------

	oSheet.Range("E1").value = "Client:" & sEngagement_Info
	oSheet.Range("E2").value = "Year End: " & sPeriod_End_Date
	'oSheet.Range("E4").value = "Prepared by: " & UserFullName
	'oSheet.Range("E5").value = "Prepared date: " & Format(DateValue(Now),"YYYY/MM/DD")
	
	

'--------------------------↓此处为导出Prescreening JE01-Additional JE04的代码-----------------------------	


'JE01
	If  Fun_File_Exist(DB_Name_JE01_01) Then 
	
	
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE01_01 ,"" ,"DBCount" )
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
		
		
		oSheet.Range("C8").value = "The start day of the end of the reporting period:" & Chr(10) &  sEnd_of_Reporting_Period_Start_Date
		oSheet.Range("F8").value = sTemp
		sTemp = GetJENumber(DB_Name_JE01_01, DOCUMENT_NUM_JE)
		oSheet.Range("E8").value =  sTemp
									
		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE01_01.xlsx"
			Call 	ExportDatabaseXLSXwithIndexforJE0104(DB_Name_JE01_01, "DB_Name_JE01_01.xlsx" , "JE01")
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE01"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE01")		
			Set oRange=oSheet2.UsedRange
			oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE01").Move After:=oBook.Sheets(oBook.Sheets.Count)

		Else 
	
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("C8").value = "The start day of the end of the reporting period:" & Chr(10) &  sEnd_of_Reporting_Period_Start_Date
		oSheet.Range("D8").value = "No record match the criteria."	
		oSheet.Range("E8").value = "N/A"
		oSheet.Range("F8").value = "N/A"	
					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
	oSheet.Range("C8").value = "The start day of the end of the reporting period:" & Chr(10) &  sEnd_of_Reporting_Period_Start_Date
	oSheet.Range("D8").value = "N/A"	
	oSheet.Range("E8").value = "N/A"
	oSheet.Range("F8").value = "N/A"	

	End If

	
'JE02 
	If  Fun_File_Exist(DB_Name_JE02_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE02_02,"" ,"DBCount" )
		
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If

		oSheet.Range("F9").value = sTemp
				sTemp = GetJENumber(DB_Name_JE02_02, DOCUMENT_NUM_JE )

		oSheet.Range("E9").value =  sTemp

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE02_02.xlsx"
			Call ExportDatabaseXLSX(DB_Name_JE02_02, "DB_Name_JE02_02.xlsx" , "JE02")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE02"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE02")		
			Set oRange=oSheet2.UsedRange
			oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE02").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("JE02").Font.Name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("D9").value = "No record match the criteria."
		oSheet.Range("E9").value = "N/A"
		oSheet.Range("F9").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
	oSheet.Range("D9").value = "N/A"	
	oSheet.Range("E9").value = "N/A"
	oSheet.Range("F9").value = "N/A"						
		
	End If


	
'JE03

	If  Fun_File_Exist(DB_Name_JE03_04) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE03_04,"" ,"DBCount" )

		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If	
	
		oSheet.Range("F10").value = sTemp
						sTemp = GetJENumber(DB_Name_JE03_04, DOCUMENT_NUM_JE )

		oSheet.Range("E10").value =  sTemp	

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE03_04.xlsx"
			Call ExportDatabaseXLSX(DB_Name_JE03_04, "DB_Name_JE03_04.xlsx" , "JE03")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE03"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE03")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE03").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("JE03").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("D10").value = "No record match the criteria."
		oSheet.Range("E10").value = "N/A"
		oSheet.Range("F10").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
		oSheet.Range("D10").value = "N/A"
		oSheet.Range("E10").value = "N/A"
		oSheet.Range("F10").value = "N/A"										
		
	End If


'JE04

	If  Fun_File_Exist(DB_Name_JE04_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE04_02,"" ,"DBCount" )

		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If	
	
		oSheet.Range("F11").value = sTemp
		sTemp = GetJENumber(DB_Name_JE04_02, DOCUMENT_NUM_JE )
		oSheet.Range("E11").value =  sTemp	

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE04_02.xlsx"
			Call ExportDatabaseXLSX(DB_Name_JE04_02, "DB_Name_JE04_02.xlsx" , "JE04")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE04"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE04")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE04").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("JE04").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("D11").value = "No record match the criteria."
		oSheet.Range("E11").value = "N/A"
		oSheet.Range("F11").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
		oSheet.Range("D11").value = "N/A"
		oSheet.Range("E11").value = "N/A"
		oSheet.Range("F11").value = "N/A"										
		
	End If



'JE05

	If  Fun_File_Exist(DB_Name_JE05_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE05_02,"" ,"DBCount" )
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
		
		'oSheet.Range("F12").value = sTemp	
		oSheet.Range("F12").value = "N/A"

		oSheet.Range("E12").value = "N/A"

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE05_02.xlsx"
			Call ExportDatabaseXLSX(DB_Name_JE05_02, "DB_Name_JE05_02.xlsx" , "JE05")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE05"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE05")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE05").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("JE05").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("D12").value = "N/A"
		oSheet.Range("E12").value = "N/A"
		oSheet.Range("F12").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
		oSheet.Range("D12").value = "The routine is not performed as the data field [Prepared by] is not mapped."
		oSheet.Range("E12").value = "N/A"
		oSheet.Range("F12").value = "N/A"										
		
	End If


'JE06

	If  Fun_File_Exist(DB_Name_JE06_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_JE06_02,"" ,"DBCount" )
		
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
		
		
		'oSheet.Range("F13").value = sTemp
		oSheet.Range("F13").value = "N/A"
		oSheet.Range("E13").value = "N/A"

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_JE06_02.xlsx"
			Call ExportDatabaseXLSXforJE06(DB_Name_JE06_02, "DB_Name_JE06_02.xlsx" , "JE06")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "JE06"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("JE06")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("JE06").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("JE06").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("D13").value = "N/A"
		oSheet.Range("E13").value = "N/A"
		oSheet.Range("F13").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
	
		oSheet.Range("D13").value = "N/A"
		oSheet.Range("E13").value = "N/A"
		oSheet.Range("F13").value = "N/A"										
		
	End If

	
	
	
'Additional JE02

	If  Fun_File_Exist(DB_Name_A_JE02_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_A_JE02_02,"" ,"DBCount" )
		
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
		
		
		oSheet.Range("C14").value = "Additional specific description:" & Chr(13) & Criteria_A_JE02
		oSheet.Range("F14").value = sTemp
		
				sTemp = GetJENumber(DB_Name_A_JE02_02, DOCUMENT_NUM_JE )

		oSheet.Range("E14").value =  sTemp	

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_A_JE02_02.xlsx"
			Call ExportDatabaseXLSXwithIndexforJE0104(DB_Name_A_JE02_02, "DB_Name_A_JE02_02.xlsx" , "A_JE02")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "A_JE02"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("A_JE02")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("A_JE02").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("A_JE02").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

		oSheet.Range("C14").value = "Additional specific description:" & Chr(13) & Criteria_A_JE02
		oSheet.Range("D14").value = "No record match the criteria."
		oSheet.Range("E14").value = "N/A"
		oSheet.Range("F14").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

		oSheet.Range("C14").value = "N/A"	
		oSheet.Range("D14").value = "N/A"
		oSheet.Range("E14").value = "N/A"
		oSheet.Range("F14").value = "N/A"							
		
	End If
	
	
	
	
'Additional JE03.1

		Criteria_A_JE03_1_DR=isplit(Criteria_A_JE03_1,"",Delimiter,1)
		Criteria_A_JE03_1_CR=isplit(Criteria_A_JE03_1,"",Delimiter,2)


	If  Fun_File_Exist(DB_Name_A_JE03_1_04) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_A_JE03_1_04,"" ,"DBCount" )
		
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
				

		oSheet.Range("C15").value = "Search journal entries debited to " & Criteria_A_JE03_1_DR & " and credited to " & Criteria_A_JE03_1_CR
		oSheet.Range("F15").value = sTemp
						sTemp = GetJENumber(DB_Name_A_JE03_1_04, DOCUMENT_NUM_JE )
		oSheet.Range("E15").value =  sTemp

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_A_JE03_1_04.xlsx"
			Call ExportDatabaseXLSXwithIndexforJE0104(DB_Name_A_JE03_1_04, "DB_Name_A_JE03_1_04.xlsx" , "A_JE03.1")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "A_JE03.1"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("A_JE03.1")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("A_JE03.1").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("A_JE03.1").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("C15").value = "Search journal entries debited to " & Criteria_A_JE03_1_DR & " and credited to " & Criteria_A_JE03_1_CR
		oSheet.Range("D15").value = "No record match the criteria."
		oSheet.Range("E15").value = "N/A"
		oSheet.Range("F15").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

		oSheet.Range("C15").value = "N/A"
		oSheet.Range("D15").value = "N/A"
		oSheet.Range("E15").value = "N/A"
		oSheet.Range("F15").value = "N/A"						
		
	End If
	

	
'Additional JE03.2

		Criteria_A_JE03_2_DR=isplit(Criteria_A_JE03_2,"",Delimiter,1)
		Criteria_A_JE03_2_CR=isplit(Criteria_A_JE03_2,"",Delimiter,2)


	If  Fun_File_Exist(DB_Name_A_JE03_2_04) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_A_JE03_2_04,"" ,"DBCount" )
		
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
		
		
		
		oSheet.Range("C16").value = "Search journal entries debited to " & 	Criteria_A_JE03_2_DR & " and credited to " & Criteria_A_JE03_2_CR
		oSheet.Range("F16").value = sTemp
								sTemp = GetJENumber(DB_Name_A_JE03_2_04, DOCUMENT_NUM_JE )
		oSheet.Range("E16").value =  sTemp

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_A_JE03_2_04.xlsx"
			Call ExportDatabaseXLSXwithIndexforJE0104(DB_Name_A_JE03_2_04, "DB_Name_A_JE03_2_04.xlsx" , "A_JE03.2")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "A_JE03.2"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("A_JE03.2")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("A_JE03.2").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("A_JE03.2").font.name = "Arial"
		Else 
		

		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("C16").value = "Search journal entries debited to " & 	Criteria_A_JE03_2_DR & " and credited to " & Criteria_A_JE03_2_CR
		oSheet.Range("D16").value = "No record match the criteria."
		oSheet.Range("E16").value = "N/A"
		oSheet.Range("F16").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

		oSheet.Range("C16").value = "N/A"
		oSheet.Range("D16").value = "N/A"
		oSheet.Range("E16").value = "N/A"
		oSheet.Range("F16").value = "N/A"						
		
	End If
	
	
	
'Additional JE04

	If  Fun_File_Exist(DB_Name_A_JE04_02) Then 
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		sTemp = GetTotal(DB_Name_A_JE04_02,"" ,"DBCount" )
	
		If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			
		End If
	
	
		oSheet.Range("C17").value = "Search journal entries with amount ended with: "& Criteria_A_JE04
		oSheet.Range("F17").value = sTemp
								sTemp = GetJENumber(DB_Name_A_JE04_02, DOCUMENT_NUM_JE )
		oSheet.Range("E17").value =  sTemp

		If sTemp > 0   Then

			strr = WorkingDirectoryExport & "DB_Name_A_JE04_02.xlsx"
			Call ExportDatabaseXLSXwithIndexforJE0104( DB_Name_A_JE04_02, "DB_Name_A_JE04_02.xlsx" , "A_JE04")
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = "A_JE04"
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item("A_JE04")		
			Set oRange=oSheet2.UsedRange
						oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
	
			For i = 1 To  20
				oSheet.Columns(i).EntireColumn.AutoFit
			Next i
			oBook.Sheets("A_JE04").Move After:=oBook.Sheets(oBook.Sheets.Count)
			oBook.Sheets("A_JE04").font.name = "Arial"
		Else 
		
		Set oSheet = oBook.Worksheets.Item("Pre-screening Report")
		oSheet.Range("C17").value = "Search journal entries with amount ended with: "& Criteria_A_JE04
		oSheet.Range("D17").value = "No record match the criteria."
		oSheet.Range("E17").value = "N/A"
		oSheet.Range("F17").value = "N/A"					
		End If
	
	Else 
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

		oSheet.Range("C17").value = "N/A"
		oSheet.Range("D17").value = "N/A"
		oSheet.Range("E17").value = "N/A"
		oSheet.Range("F17").value = "N/A"						
		
	End If

'-------------------导出完成，保护工作簿，保存并退出Excel----------------------------------------------
	
	Set oSheet = oBook.Worksheets.Item("Pre-screening Report")

	osheet.select
	oSheet.Range("A:Z").EntireColumn.AutoFit
	
	PW  = Client.ManagedProject & " - " & iSplit(sFilename,"","\",1,1)
	oSheet.Protect PW, DrawingObjects:=True, Contents:=True, Scenarios:=True
		
	oBook.Save
	oBook.Close (True)
	excel.Quit
	Set oRange = Nothing
	Set oSheet = Nothing
	Set oBook = Nothing
	Set excel=Nothing

	
	

End Function










Function Step4_Export_Further_Criteria

	Dim sTemp As String
	Dim db  As database
	Dim rs As recordset
	Dim ThisTable As Object
	Dim field As field
	Dim rec As Object
	Dim i As Long
	Dim j As Integer
	Dim k As Integer
	Dim iFieldCount As Integer
	Dim P As Integer
	Dim CriteriaLog(10) As String
	
	S1Check = 0

	strr= sTempExcelSource  & "\CriteriaSelectionReport.xlsx"   '
	dstr = WorkingDirectoryExport &  sEngagement_Info & "_" & Format(Now, "yyyymmdd") & Format(Now, "hhmmss") & "_CriteriaSelectionReport.xlsx"
	If Len( dstr ) > 218 Then
		MsgBox "您设置的Project名称太长，将导致生成的Report错误。请在Mapping处的[sEngagement_Info]中重新设定一个较短的名称再尝试"
		End
	Else
	End If

	
	
	FileCopy strr, dstr
	
	Set excel = CreateObject("Excel.Application")
	Set oBook = excel.Workbooks.Open(dstr)
	Set oSheet = oBook.Worksheets.Item("Summary Information")
	
	'sMsg = "Processing - Export generation information to Excel..."

	oSheet.Range("C1").value = "Client:" & sEngagement_Info
	oSheet.Range("D1").value = "Year End: " & sPeriod_End_Date
	'oSheet.Range("D1").value = "Prepared by: " & UserFullName
	'oSheet.Range("D2").value = "Prepared date: " & Format(DateValue(Now),"YYYY/MM/DD")
		
	Set fs = CreateObject("Scripting.FileSystemObject")  
				
			oSheet.Cells.EntireRow.AutoFit
			 
	For i = 1 To 99
		If  fs.FileExists(DataWorkingDirectory & FurtherCriteria & i & ".idm"  ) Then

			Set oSheet = oBook.Worksheets.Item("Summary Information")
			oSheet.Range("A" & i + 4).value = i 
			oSheet.Range("B" & i + 4).value = FurtherCriteria & i  
			
		Set db = Client.OpenDatabase	(FurtherCriteria & i & ".idm")
		Set table = db.TableDef 
		Set task = db.TableManagement
		
		
			t = 0

		For k = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
			Set field = table.GetFieldAt(k) 
			If field.name = DOCUMENT_NUM_JE   Then
			t= t+1
			
			End If
		Next k	
		
			If t > 0 Then
		
		
			oSheet.Range("D" & i + 4).value = GetTotal( FurtherCriteria & i & ".idm"   , "" , "DBCount")
			
			Set db = Client.OpenDatabase(FurtherCriteria & i & ".idm")
			Set task = db.Summarization
			task.AddFieldToSummarize DOCUMENT_NUM_JE 
			dbName = "Temp-Summarization.IDM"
			task.OutputDBName = dbName
			task.CreatePercentField = FALSE
			task.PerformTask
			Set task = Nothing
			Set db = Nothing
			Client.OpenDatabase (dbName)		
		
			sTemp =  GetTotal("Temp-Summarization.IDM","" ,"DBCount" )
			If sTemp > 1048576 Then
			MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			End If
	
				If sTemp >2500 Then
				
					sTemp = ">2500"				
				End If
				
			oSheet.Range("C" & i + 4).value =  sTemp
			
			Fun_Delete_File("Temp-Summarization.IDM")
			
			
			
			Else
			
			sTemp =  GetTotal(FurtherCriteria & i & ".idm","" ,"DBCount" )

			
			If sTemp > 1048576 Then
				MsgBox "检测到数据行数超出Excel限制，导出Report后请检查数据是否完整并手工修改"
			End If
			
			oSheet.Range("C" & i + 4).value = "N/A"
			oSheet.Range("D" & i + 4).value = "N/A"
			End If
		
			strr = WorkingDirectoryExport & FurtherCriteria & i & ".xlsx"

			Call ExportDatabaseXLSX(FurtherCriteria & i & ".idm", FurtherCriteria & i  & ".xlsx" , FurtherCriteria & i)
			
			Set oSheet = oBook.Worksheets.Add
			oSheet.Name = FurtherCriteria & i
			Set oBook2=excel.Workbooks.Open(strr)
			Set oSheet2=oBook2.Worksheets.item(FurtherCriteria & i)
			Set oRange=oSheet2.UsedRange
			oRange.Font.Name = "Arial"
			oRange.Font.size = 10
			oRange.Copy
			oSheet.Paste 
			oBook2.Save
			oBook2.Close (True)
			Kill strr 
			
			
			For j = 1 To  20
				oSheet.Columns(j).EntireColumn.AutoFit
			Next j
			
			oBook.Sheets(FurtherCriteria & i).Move After:=oBook.Sheets(oBook.Sheets.Count)
		End If 
		
	Next i
	
	fs = Nothing
	
	
	Set oSheet = oBook.Worksheets.Item("Summary Information")
	oSheet.Activate
			
	oBook.Save
	oBook.Close (True)
	excel.Quit
	Set oRange = Nothing
	Set oSheet = Nothing
	Set oBook = Nothing
	Set excel = Nothing

	
	'Set runScript =  CreateObject("WScript.Shell")
	'runScript.Run "explorer.exe /e," & client.WorkingDirectory & "Exports.ILB"
	
End Function




Function Rerun_Step1_Validation
							client.closeall
					           	Call Fun_Delete_File(Null_GL_Account_IDM)
							Call Fun_Delete_File(Null_GL_Number_IDM)
							Call Fun_Delete_File(Null_GL_Description_IDM)
							Call Fun_Delete_File(NotinPeriod_PostDate_IDM)
							Call Fun_Delete_File("Summary by Doc No.IDM")
							Call Fun_Delete_File(List_of_accounts_with_variance_IDM)
							Call Fun_Delete_File(Null_TB_account_number_IDM)
							Call Fun_Delete_File(Null_TB_account_name_IDM)
							Call Fun_Delete_File(Duplicate_AccountCode_TB_IDM)
							Call Rerun_step1_Completeness_Check
	                			
End Function	                	

Function Rerun_step1_Completeness_Check

							client.closeall
			                		Call Fun_Delete_File(DB_Name00_GLwithAccName)
							Call Fun_Delete_File(DB_Name01_SumJEbyAccNo)
							Call Fun_Delete_File(DB_Name02_CompletenessTest)
							Call Fun_Delete_File ("AccountMappingTable.IDM")
							Call Rerun_Step2_AccountMapping

End Function
                				
Function	Rerun_Step2_AccountMapping
					
                				
							client.closeall
							Call Fun_Delete_File(AccountMapping_File_name) 
							Call Rerun_Step3_Pre_Screening
End Function
						
Function	 Rerun_Step3_Pre_Screening

							client.closeall
							Call Fun_Delete_File(DB_Name03_GLwithAccountMapping)
							Call Fun_Delete_File(DB_Name04_SampleGL)
							
							
							Call Fun_Delete_File(DB_Name_JE01_01)
							Call Fun_Delete_File(DB_Name_JE02_01)
							Call Fun_Delete_File(DB_Name_JE02_02)
							Call Fun_Delete_File(DB_Name_A_JE02_01)
							Call Fun_Delete_File(DB_Name_A_JE02_02)
							Call Fun_Delete_File(DB_Name_JE03_01)
							Call Fun_Delete_File(DB_Name_JE03_02)
							Call Fun_Delete_File(DB_Name_JE03_03)
							Call Fun_Delete_File(DB_Name_JE03_04)
							Call Fun_Delete_File(DB_Name_A_JE03_1_01)
							Call Fun_Delete_File(DB_Name_A_JE03_1_02)
							Call Fun_Delete_File(DB_Name_A_JE03_1_03)
							Call Fun_Delete_File(DB_Name_A_JE03_1_04)
							Call Fun_Delete_File(DB_Name_A_JE03_2_01)
							Call Fun_Delete_File(DB_Name_A_JE03_2_02)
							Call Fun_Delete_File(DB_Name_A_JE03_2_03)
							Call Fun_Delete_File(DB_Name_A_JE03_2_04)
							Call Fun_Delete_File(DB_Name_JE04_01)
							Call Fun_Delete_File(DB_Name_JE04_02)
							Call Fun_Delete_File(DB_Name_A_JE04_01)
							Call Fun_Delete_File(DB_Name_A_JE04_02)
							Call Fun_Delete_File(DB_Name_JE05_01)
							Call Fun_Delete_File(DB_Name_JE05_02)
							Call Fun_Delete_File(DB_Name_JE06_01)
							Call Fun_Delete_File(DB_Name_JE06_02)
	
							
							Call Fun_Delete_File(PRESCR_R1)
							Call Fun_Delete_File(PRESCR_R2)
							Call Fun_Delete_File(PRESCR_R3)
							Call Fun_Delete_File(PRESCR_R4)
							Call Fun_Delete_File(PRESCR_A2)
							Call Fun_Delete_File(PRESCR_A3_1)
							Call Fun_Delete_File(PRESCR_A3_2)
							Call Fun_Delete_File(PRESCR_A4)
							Call Fun_Delete_File(DB_NAME_BI_01)
							Call Fun_Delete_File(DB_NAME_BI_02)
							Call Fun_Delete_File(DB_NAME_BI_03)
							Call Fun_Delete_File(DB_NAME_BI_04)
							Call Fun_Delete_File(DB_NAME_BI_05)
							Call Fun_Delete_File(DB_NAME_BI_06)
							Call Fun_Delete_File(DB_NAME_BI_07)
							Call Fun_Delete_File(DB_NAME_BI_FinalPeriod)
							Call Fun_Delete_File(DB_NAME_BI_FinalPopulation)								
End Function



Function FieldCheck (DBname As String, sfieldname As String, fieldtype As String)

	If  Fun_File_Exist( DBname ) Then 
	
		Set db = Client.OpenDatabase	(DBname)
		Set table = db.TableDef 
		Set task = db.TableManagement
		t = 0
		For i = table.count To 1 Step -1	'这里一定要用倒序，如果用正序的话，删除字段后，table.count记录的数据与实际的字段数不一致，会报错
			Set field = table.GetFieldAt(i) 
			If field.name = sfieldname  Then
			 t =i
			End If
		Next i	
		
		If t>0 Then
		
			field = table.GetFieldAt(t) 
		
			Select Case fieldtype
			
				Case "character"
				
				F = field.IsCharacter
				
				Case "Numeric"
	
				F = field.IsNumeric
				
				Case "Date"
				
				F = field.Isdate
				
				Case "Any"
				
				F = "true"
				
			End Select
		
			If F <> "true" Then
			
				d= d+1
			
				MsgBox "必要的" & sfieldname & "列在" & DBname & "数据库中格式不正确，请检查"
			
			End If
		
		
		Else
		
			d = d + 1
		
			MsgBox "必要的" & sfieldname & "列在" & DBname & "数据库中不存在，请检查"
			
		End If
		
		
		Set field = Nothing
		Set table = Nothing	
		Set task = Nothing
		Set db = Nothing

	Else
	
	MsgBox "必要的" & DBname & "数据库不存在，请检查"

	MsgBox "Script将退出，请检查后再次运行"

	
	End
	
	End If
	
End Function




Function checkValidationFields
	d = 0

		Call  FieldCheck(GL_File_name,DOCUMENT_NUM_JE,"Character")
		Call  FieldCheck(GL_File_name,ACCOUNT_NUM_JE,"Character")
		Call  FieldCheck(GL_File_name,ACCOUNT_DESCRIPTION_JE,"Character")
		Call  FieldCheck(GL_File_name,DESCRIPTION_JE,"Character")
		'Call  FieldCheck(GL_File_name,Dr_Amount_JE,"Numeric")
		'Call  FieldCheck(GL_File_name,Cr_Amount_JE,"Numeric")
		Call  FieldCheck(GL_File_name,Amount_JE,"Numeric")
		Call  FieldCheck(GL_File_name,POSTING_DATE_JE,"Date")
		Call  FieldCheck(TB_File_Name,ACCOUNT_NUM_TB,"Character")
		'Call  FieldCheck(TB_File_Name,ACCOUNT_DESCRIPTION_TB,"Character")
		Call  FieldCheck(TB_File_Name,Opening_Balance_TB,"Numeric")
		Call  FieldCheck(TB_File_Name,Ending_Balance_TB,"Numeric")
		'Call  FieldCheck(TB_File_Name,diff,"Numeric")
	
	If d >0  Then
	
	MsgBox "Script将退出，请检查后再次运行"
	
	End

	End If


End Function


Function checkAccountMappingFields


	d = 0

		Call  FieldCheck(AccountMapping,"COMB_ACCOUNT_NUM","Character")
		Call  FieldCheck(AccountMapping,"COMB_ACCOUNT_DESC","Character")

	If d >0  Then
	
	MsgBox "Script将退出，请检查后再次运行"

	End
	
	End If


End Function


Function checkPrescreeningFields

	d = 0
		If UESR_JE <> "" Then
		
		Call  FieldCheck(GL_File_name,UESR_JE,"Character")

		End If 
		
		If IS_MANUAL_JE <> "" Then
		
		Call  FieldCheck(GL_File_name,IS_MANUAL_JE,"Character")

		End If 
		

		Call  FieldCheck(AccountMapping_File_name,"GL_NUMBER","Character")
		Call  FieldCheck(AccountMapping_File_name,"GL_NAME","Character")
		Call  FieldCheck(AccountMapping_File_name,STANDARDIZED_ACCOUNT_NAME_AM,"Character")

		Call  FieldCheck(GL_File_name,DOCUMENT_NUM_JE,"Character")
		Call  FieldCheck(GL_File_name,ACCOUNT_NUM_JE,"Character")
		Call  FieldCheck(GL_File_name,ACCOUNT_DESCRIPTION_JE,"Character")
		Call  FieldCheck(GL_File_name,DESCRIPTION_JE,"Character")
		Call  FieldCheck(GL_File_name,Amount_JE,"Numeric")
		Call  FieldCheck(GL_File_name,POSTING_DATE_JE,"Date")
		
		

	If d >0  Then
	
	MsgBox "Script将退出，请检查后再次运行"

	End
	
	End If


End Function
