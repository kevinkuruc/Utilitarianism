# Utilitarianism

Here is an outline of how to use the Mimi-RICE code for our Utilitarian paper:
Will Need:
Mimi.jl downloaded
DataFrames.jl downloaded
CSV.jl downloaded
NLopt.jl downloaded
To download Mimi, for example, go to Julia and type the command Pkg.add(“Mimi.jl”)
The main files for solving:
“Results_Utilitarian.jl”
“Results_CostMin.jl”
--running these will produce the csv files that have data on everything we eventually plot using STATA. Right now they have made the following changes to RICE2010:
1) Population numbers are fixed to match updated data (I went directly into RICE excel worksheet to replace these)
2) Savings rates have been changed to 0.258 rather than the data in excel. This was done in the parameters.jl files. I have commented out the reading in of savings rates and instead have put in a 60x12 matrix of 0.258.
3) Social rate of discount is changed to .008. You can see this in the Results_Utilitarian.jl file.. after calling the RICE model as its dictated in the excel sheet it changes the parameter using 'setparameter()' function.

Important sub-codes:
Optimize_UtilitarianRICE.jl
Optimize_CostMinRICE.jl
---These are the routines I’ve written to maximize welfare in RICE. They are subtly different in their guts. For Optimize_UtilitarianRICE the routine searches for each MIU (fraction mitigation) by country-region. This way I can put the obvious and easy constraints on the problem of lower bound 0, upper bound 1, which makes the optimization intuitive. Since MIU and price are linked 1:1 I can just back out the prices that implied this choice. For Optimize_CostMinRICE I instead have the algorithm look for a price, then plug that price in for each region in that time period, then back out MIU with a constraint here on a regions MIU being bigger than zero, less than 1. There will be a multiplicity of solutions, since once the model wants 0 MIU for everyone, infinitely many prices satisfy this (just make it sufficiently high). This is fine, the allocations are what matter for the solutions. You have to slightly rearrange the variables and emissions component in gathering RICE, which is why the entire “Optimize_CostMinRICE.jl” has different parameter files and components, etc. 
Rice Model:
Rice2010_Utilitarian.jl: takes components and compiles them into a Mimi model. Defines a function ‘getRice_Utilitarian’ that takes as an input getRice_Utilitarian(Negishi=true) [or false of course, which is how we generate all of our results, in fact Utilitarian with Negishi weights makes little sense] that will construct and solve the model with or without Negishi weights
Rice2010_CostMin.jl: Does the same for the cost-min version
Components Folders: Defines each component. See Mimi codes on GitHub for tutorial on this way of compiling IAMs.
Data:
Parameters_Utilitarian.jl: Reads in the data file to the parameters of the Mimi Model
Parameters_CostMin.jl: does the same for the one price version
The RICE worksheet must be in a separate folder labelled DATA at the same folder level as the “src” one with the code within it. The parameter file wants to look here for the data.
Misc:
RunBAU.jl : Runs RICE with no climate policy
Helpers.jl: defines functions that go in and read out data from excel into the format mimi likes. As long as this is kept in the src folder it shouldn’t need to be edited or opened.
Note: We begin plotting in 2019 not 2015, so we just take a convex combination of our solutions for 2015 and 2025 to approximate 2019.
