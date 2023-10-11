# DA263 Getting Started: Setup, Introduction and Start of the project
In this exercise, you will set up your environment for the workshop

## Login to the system

After completing these steps you will have configured a SAP Business Application Studio for this exercise.

1.	[SAP Business Application Studio] (https://da263-pj0569xc.authentication.ap11.hana.ondemand.com/login)

<br>![](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/LoginScreen.png)

### USER:       DA263-XXX@education.cloud.sap    -> XXX is depending on our seat: 001 .. 045
### PASSWORD:   Acce$$teched23

<BR> ![](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/Credentials.png)

<BR> Now you reach the SAP Business Application Studio which may or may not contain already so called DEVELOPMENT SPACES

<BR> ![](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/DEV_SPACE_NEW.png)

<BR> Select "Create Dev Space"

<BR> ![](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/MyDevSpace.png)

<BR> Choose a NAME of your choice (without spaces)
<BR> Select SAP HANA NATIVE APPLICATION

<BR> ![](/Exercises_Content/9_0_HC_Intro/IMAGES_DA263/MyDevSpaceStart.png)
<BR> Spaces have states as

 - STOPPED
 - STARTING
 - RUNNING

 After a certain period of non usage a space will close and after that it will automatical stopp
You will loose no content. The whole content will be saved and restored.


2.	Insert this code.
``` sql
 DATA(params) = request->get_form_fields(  ).
 READ TABLE params REFERENCE INTO DATA(param) WITH KEY name = 'cmd'.
  IF sy-subrc <> 0.
    response->set_status( i_code = 400
                     i_reason = 'Bad request').
    RETURN.
  ENDIF.
```

## Summary

Now that you have completed the set up

 - Continue to - [Exercise 1 Getting Started](/Exercises_Content/9_0_HC_Intro/1_DBX_Getting_Started.md)
 - Continue to - [Exercise 2 Introduction](/Exercises_Content/9_0_HC_Intro/2_DBX_Introduction.md)
 - Continue to - [Exercise 3 Introduction](/Exercises_Content/9_0_HC_Intro/3_Start_our_project.md)
 - Continue to - [Main page](../../README.md)
