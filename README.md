# triaxread
This code aims to read and retrieve important parameters of soil mechanics from triaixial test.

During the study of soft soil in soil mechanics, is vital to understand the behaviour of deformation and the stiffness of such material.
In reality, a lot of important parameters are retrieved by performing a triaxial test. And the most common test in laboratories is the consolidated undrained (CU) test. Usually, test apparatus returns data in a .csv file. I will make use of this .csv file of both CD and CU and analyse them. 

Although, I explain just briefly how to perform a triaxial test, this code is not a guide of such. Also, I mention some relevant mechanical parameters, which are not fully explained closely. Some previuos knowledge on soil physics is useful to fully understand these entries.

## Scope

This code aims to analyse and compute the most important soil parameters used in academia and construction industry from a CU triaxial test.

Basically, this test works in two phases: one of consolidation, where the sample is subjected to an isotropic stress; and a second one of vertical stress or shearing where anisotropy
in the material is applied. In the second phase, two possible options can follow: one where the excess pore pressure in the sample is avoided (Drained); or another where
pore pressure changes is allowed (Undrained). The latter is known as CU test, or consolidated undrained test. After the test analysis undrained shear strength and friction angle can be calculated.

In this repository, I am going to explain how to handle triaixial test data in order to be able to separate the data in two sets: one with consolidation phase and a second with 
shear phase. 



