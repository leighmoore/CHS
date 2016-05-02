*********************************************
* 											
* 	ACCOUNTABLE COMMUNITIES FOR HEALTH
*	CHIS DATA, ADULT
*											
*	KRISTIN MOORE							
*	FEBRUARY 2016							
*											
*********************************************;

libname CHIS0514 "R:\Working Folder\CHIS data\CHIS2005-CHIS2014_SonomaCty_November2015";
*libname CHIS0514 "R:\Transfer\ACH";

*************************
	CREATING NEW VARIABLES;

data adult;
	set CHIS0514.chis0514_adult;

*Age categories - 4;
	if year in(2013:2014) then do;
		if srage_p1 in(18:29) then age = 1;
		else if srage_p1 in(30:44) then age = 2;
		else if srage_p1 in(45:64) then age = 3;
		else if srage_p1 in(65:85) then age = 4;
	end;
	if year in(2011:2012) then do;
		if srage_p in(18:29) then age = 1;
		else if srage_p in(30:44) then age = 2;
		else if srage_p in(45:64) then age = 3;
		else if srage_p in(65:85) then age = 4;
	end;

*Age categories - 65;
	if age in(1:3) then age2 = 1;
	else if age in(4) then age2 = 2;

*Education;
	if year in(2013:2014) then do;
		if ahedc_p1 in(1:2) then educ = 1;
		else if ahedc_p1 in(3) then educ = 2;
		else if ahedc_p1 in(4:10) then educ = 3;
	end;
	if year in(2011:2012) then do;
		if aheduc in(1:2) then educ = 1;
		else if aheduc in(3) then educ = 2;
		else if aheduc in(4:10) then educ = 3;
	end;

*Latino;
	if racedf_s in(1) then latino = 1;
	else if racedf_s in(2:4) then latino = 0;

*Medi-Cal cutoffs;
	if year in(2013:2014) then do;
		if povll_aca in(1) then fplmed = 1;
		else if povll_aca in(2:3) then fplmed = 2;
		else if povll_aca in(4) then fplmed = 3;
	end;
	if year in(2011:2012) then do;
		if povll2_p ge 0.00 and povll2_p le 1.38 then fplmed = 1;
		else if povll2_p ge 1.39 and povll2_p le 3.99  then fplmed = 2;
		else if povll2_p ge 4.00 then fplmed = 3;
	end;

*Diabetes;
	if ab22 = 1 then diab = 1;
	else if ab22 in(2:3) then diab = 2;

*Obesity;
	if rbmi in(4) then obese = 1;
	else obese = 2;

*Soda drinking per week;
	if year in(2013:2014) then do;
		if aesoda_p1 = 0 then soda = 0;
		else if aesoda_p1 in(1:2) then soda = 1;
		else if aesoda_p1 in(3:4) then soda = 2;
		else if aesoda_p1 in(5:6) then soda = 3;
	end;
	if year in(2011:2012) then do;
		if ae_soda = 0 then soda = 0;
		else if ae_soda in(1:3) then soda = 1;
		else if ae_soda in(4:13) then soda = 2;
		else if ae_soda ge 14 then soda = 3;
	end;
	
*Drank soda in the last week;
	if soda ne 0 then soda2 = 1;
	else soda2 = 2;

*Fast food eating per week;
	if year in(2013:2014) then do;
		if ac31_p1 = 0 then ff = 0;
		else if ac31_p1 in(1:2) then ff = 1;
		else if ac31_p1 in(3:9) then ff = 2;
	end;
	if year in(2011:2012) then do;
		if ac31 = 0 then ff = 0;
		else if ac31 in(1:2) then ff = 1;
		else if ac31 ge 3 then ff = 2;
	end;

*Fast food eating per week;
	if ff ne 0 then ff2 = 1;
	else ff2 = 2;

*Walked for leisure at least 150 minutes in last week;
	if ad42w in(-1:149) then exer = 1;
	else if ad42w in(150:720) then exer = 2;

	sex = srsex;
	heart = ab34;
	hfail = ab52;
	hbp = ab29; 
	ins64 = ins64_p;
	medical = insmd;

	label educ = "Highest educational attainment";
	label age2 = "Age - 0-64, 65+";
	label age = "Age";
	label latino = "Latino/Hispanic ethnicity";
	label fplmed = "Federal Poverty Level - 0-138%, 139-399%, 400%+";
	label sex = "Sex";
	label heart = "Ever diagnosed with heart disease";
	label hfail = "Among those ever diagnosed with heart disease, ever told have heart failure or congestive heart failure";
	label diab = "Ever diagnosed with diabetes";
	label smkcur = "Current smoker";
	label obese = "Obese (BMI 30+)";
	label binge12 = "Binge drank at least once in the l2 months";
	label soda = "Number of times drank soda per week";
	label soda2 = "Drank soda at least once per week";
	label ff = "Fast food per week";
	label ff2 = "Ate fast food at least once per week";
	label exer = "Walked for at least 150 minutes per week for leisure";
	label ins64 = "Health insurance coverage among <65 years";
	label ins65 = "Health insurance coverage among >=65 years";
	label medical = "Insured by Medi-Cal";

run;

data adult;
	set adult;

*High blood pressure;
	if ab29 = 2 or ab29 = 3 then hbp = 2;
	else hbp = 1;

	label hbp = "Ever diagnosed with high blood pressure";
run;

proc format;
	value ins64f
			-1 = 'Inapplicable - >= 65 years'
			1 = 'Uninsured'
			2 = 'Medi-Cal'
			3 = 'Medicare'
			4 = 'Employment-based'
			5 = 'Privately purchased'
			6 = 'CHIP/Other public program';
	value ins65f
			-1 = 'Inapplicable - <65 years'
			1 = 'Medicare & Medi-Cal'
			2 = 'Medicare + other'
			3 = 'Medicare'
			4 = 'Other only'
			5 = 'Uninsured';
	value sodaf
			0 = '0 times'
			1 = '1-3 times'
			2 = '4-13 times'
			3 = '14+ times';
	value fff
			0 = '0 times'
			1 = '1-2 times'
			2 = '3+ times';
	value smkcurf
			1 = 'Current smoker'
			2 = 'Not current smoker';
	value yesnof
			1 = 'Yes'
			2 = 'No';
	value hfailf
			1 = 'Yes'
			2 = 'No'
			-1 = 'Never diagnosed with heart disease';
	value agef
			1 = '18-29'
			2 = '30-44'
			3 = '45-64'
			4 = '65+';
	value age2f
			1 = '18-64'
			2 = '65+';
	value educf
			1 = 'Less than HS'
			2 = 'HS or equivalent'
			3 = 'More than HS';
	value latinof
			1 = 'Latino'
			0 = 'Non-Latino';
	value sexf
			1 = 'Male'
			2 = 'Female';
	value fplmedf
			1 = '0-138%'
			2 = '139-399%'
			3 = '400%+';
run;

data adult;
	set adult;
	format age agef. educ educf. latino latinof. sex sexf. age2 age2f. fplmed fplmedf.
	heart yesnof. hbp yesnof. diab yesnof. hfail hfailf. smkcur smkcurf. obese yesnof. soda sodaf. ff fff. 
	ff2 yesnof. soda2 yesnof. exer yesnof. ins yesnof. ins64 ins64f. ins65 ins65f. medical yesnof.;
run;




**************
	ANALAYZING VARIABLES AND SENDING OUTPUT TO .DOC;
%macro catvar(title,dataset,catvar);
ods rtf toc_data;
ods noptitle;
ods proclabel=&title;
proc sort data = &dataset;
	by &catvar;
run;
proc surveyfreq data=&dataset varmethod=jackknife nosummary; 
	weight rakedw0; 
	repweight rakedw1--rakedw80/JKCOEFS=1;
	tables &catvar/cv nofreq nostd; 
	title5 &title;
	where year in(2011:2014);
run;
%mend;

%macro bivar(title,dataset,bivar1,bivar2);
ods noptitle;
ods rtf toc_data;
ods proclabel=&title;
proc sort data = &dataset;
	by &bivar1 &bivar2;
run;
proc surveyfreq data=&dataset varmethod=jackknife nosummary; 
	weight rakedw0; 
	repweight rakedw1--rakedw80/JKCOEFS=1;
	tables &bivar1*&bivar2/cvwt row cv wchisq nofreq nostd; 
	title5 &title;
	where year in(2011:2014);
run;
%mend;

ods rtf file = "R:\Transfer\ACH\1114_ACHIndicators_CHIS_20160205.rtf" style=Custom contents=yes;
options nocenter pageno=1 nodate;
title j=c height=4 Font=Calibri Bold 'INDICATORS - ACCOUNTABLE COMMUNTIES FOR HEALTH';
footnote j=left height=2 Italic "Data from 2011-2014; adults 18 years and older; California Health Interview Survey; CV = coefficient of variation
 which is a measure of reliability (CV .0-.29 = good, CV .30-.49 = unreliabile, CV .5-1.0 = must be suppressed)";
ods rtf startpage=now;
title2 j=left '  ';
title3 j=left height=4 'DEMOGRAPHICS';
title4 j=left '  ';
%catvar('D1. Age categories',adult,age);
%catvar('D2. Age categories, +/- 65 years',adult,age2);
%catvar('D3. Highest educational attainment',adult,educ);
%catvar('D4. Latino or Hispanic',adult,latino);
%catvar('D5. Participant sex',adult,sex);
%catvar('D6. Federal Poverty Level',adult,fplmed);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH INSURANCE STATUS';
%catvar('I1. Current health insurance status',adult,ins);
%catvar('I2. Type of health insurance, 18-64 years',adult,ins64);
%catvar('I3. Type of health insurance, 65+ years',adult,ins65);
%catvar('I4. Receives Medi-Cal benefits',adult,medical);
%bivar('I5. Age categories by current health insurance status',adult,age,ins);
%bivar('I6. Age (+/- 65 years) by current health insurance status',adult,age2,ins);
%bivar('I7. Highest educational attainment by current health insurance status',adult,educ,ins);
%bivar('I8. Latino or Hispanic by current health insurance status',adult,latino,ins);
%bivar('I9. Sex by current health insurance status',adult,sex,ins);
%bivar('I10. Federal Poverty Level by current health insurance status',adult,fplmed,ins);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH BEHAVIORS - CURRENT SMOKER';
%catvar('C1. Current smoker',adult,smkcur);
%bivar('C2. Age by current smoker',adult,age,smkcur);
%bivar('C3. Age categories (+/- 65 years) by current smoker',adult,age2,smkcur);
%bivar('C4. Highest educational attainment by current smoker',adult,educ,smkcur);
%bivar('C5. Latino or Hispanic by current smoker',adult,latino,smkcur);
%bivar('C6. Sex by current smoker',adult,sex,smkcur);
%bivar('C7. Federal Poverty Level by current smoker',adult,fplmed,smkcur);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH BEHAVIORS - ATE FAST FOOD';
%catvar('F1. Ate fast food in the last week',adult,ff2);
%bivar('F2. Age by eating fast food in the last week',adult,age,ff2);
%bivar('F3. Age (+/- 65 years) by eating fast food in the last week',adult,age2,ff2);
%bivar('F4. Highest educational attainment by eating fast food in the last week',adult,educ,ff2);
%bivar('F5. Latino or Hispanic by eating fast food in the last week',adult,latino,ff2);
%bivar('F6. Sex by eating fast food in the last week',adult,sex,ff2);
%bivar('F7. Federal Poverty Level by eating fast food in the last week',adult,fplmed,ff2);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH BEHAVIORS - SODA';
%catvar('S1. Drank soda in the last week',adult,soda2);
%bivar('S2. Age by drinking soda in the last week',adult,age,soda2);
%bivar('S3. Age (+/- 65 years) by drinking soda in the last week',adult,age2,soda2);
%bivar('S4. Highest educational attainment by drinking soda in the last week',adult,educ,soda2);
%bivar('S5. Latino or Hispanic by drinking soda in the last week',adult,latino,soda2);
%bivar('S6. Sex by drinking soda in the last week',adult,sex,soda2);
%bivar('S7. Federal Poverty Level (by drinking soda in the last week',adult,fplmed,soda2);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH BEHAVIORS - PHYSICAL ACTIVITY';
%catvar('P1. Walked at least 150 minutes for leisure in the last week',adult,exer);
%bivar('P2. Age by walked at least 150 minutes for leisure in the last week',adult,age,exer);
%bivar('P3. Age (+/- 65 years) by walked at least 150 minutes for leisure in the last week',adult,age2,exer);
%bivar('P4. Highest educational attainment by walked at least 150 minutes for leisure in the last week',adult,educ,exer);
%bivar('P5. Latino or Hispanic by walked at least 150 minutes for leisure in the last week',adult,latino,exer);
%bivar('P6. Sex by walked at least 150 minutes for leisure in the last week',adult,sex,exer);
%bivar('P7. Federal Poverty Level by walked at least 150 minutes for leisure in the last week',adult,fplmed,exer);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH STATUS - HEART DISEASE';
%catvar('H1. Ever diagnosed with heart disease',adult,heart);
%catvar('H1a. Among those with heart disease, ever told have heart failure or congestive heart failure',adult,hfail);
%bivar('H2. Age by ever diagnosed with heart disease',adult,age,heart);
%bivar('H3. Age (+/- 65 years) by ever diagnosed with heart disease',adult,age2,heart);
%bivar('H4. Highest educational attainment by ever diagnosed with heart disease',adult,educ,heart);
%bivar('H5. Latino or Hispanic by ever diagnosed with heart disease',adult,latino,heart);
%bivar('H6. Sex by ever diagnosed with heart disease',adult,sex,heart);
%bivar('H7. Federal Poverty Level by ever diagnosed with heart disease',adult,fplmed,heart);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH STATUS - HIGH BLOOD PRESSURE';
%catvar('BP1. Ever diagnosed with high blood pressure',adult,hbp);
%bivar('BP2. Age by ever diagnosed with high blood pressure',adult,age,hbp);
%bivar('BP3. Age (+/- 65 years) by ever diagnosed with high blood pressure',adult,age2,hbp);
%bivar('BP4. Highest educational attainment by ever diagnosed with high blood pressure',adult,educ,hbp);
%bivar('BP5. Latino or Hispanic by ever diagnosed with high blood pressure',adult,latino,hbp);
%bivar('BP6. Sex by ever diagnosed with high blood pressure',adult,sex,hbp);
%bivar('BP7. Federal Poverty Level by ever diagnosed with high blood pressure',adult,fplmed,hbp);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH STATUS - DIABETES';
%catvar('D1. Ever diagnosed with diabetes',adult,diab);
%bivar('D2. Age by ever diagnosed with diabetes',adult,age,diab);
%bivar('D3. Age (+/- 65 years) by ever diagnosed with diabetes',adult,age2,diab);
%bivar('D4. Highest educational attainment by ever diagnosed with diabetes',adult,educ,diab);
%bivar('D5. Latino or Hispanic by ever diagnosed with diabetes',adult,latino,diab);
%bivar('D6. Sex by ever diagnosed with diabetes',adult,sex,diab);
%bivar('D7. Federal Poverty Level by ever diagnosed with diabetes',adult,fplmed,diab);
title3 j=left height=4 'DEMOGRAPHICS BY HEALTH STATUS - OBESITY';
%catvar('O1. Obese (30+ BMI)',adult,obese);
%bivar('O2. Age by obese',adult,age,obese);
%bivar('O3. Age (+/- 65 years) by obese',adult,age2,obese);
%bivar('O4. Highest educational attainment by obese',adult,educ,obese);a
%bivar('O5. Latino or Hispanic by obese',adult,latino,obese);
%bivar('O6. Sex by obese',adult,sex,obese);
%bivar('O7. Federal Poverty Level by obese',adult,fplmed,obese);

ods rtf close;


