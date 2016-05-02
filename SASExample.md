*********************************************
* 											
* 	ACCOUNTABLE COMMUNITIES FOR HEALTH
*	CHIS DATA, ADULT
*											
*	KRISTIN MOORE							
*	FEBRUARY 2016							
*											
*********************************************;

/*Lets add some labeling here too*/

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


