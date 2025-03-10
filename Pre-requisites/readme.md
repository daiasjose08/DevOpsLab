# Prerequistes

To enable a smooth lab experience, we require you to have completed some pre-workshops activities.

### Create / have access to 3 environments

@daiasjose08
Depending on your employer, you may have the environments already, but the enviornments do need to be seperate to your current environments to prevent inadverdent moving of solutions around and corruption

Microsoft provides all users the ability to have 3 developer environments which are perfect for our lab.

## Create Azure DevOps

@mnecker

## Install VS Code

VS Code is a code editor provided by Microsoft which we will use to complete some automated testing.

1. Navigate to <https://code.visualstudio.com/download> and download and install VSCode version appropriate for your machine.

## Import a sample solution and data

To accomodate our lab, we will re-use a solution that has been prepared for you. This will be set up in your Developer environment and moved through the environments by the pipelines you create in the lab.

1. Download the unmanaged solution for a Accomodation listing app from <https://linked365.blog/CPSDevOpsSolution_1_0_0_3.zip>
2. Download the data files from <https://linked365.blog/Listing_Files.zip> and extract the files into a folder.
3. Import the Solution in your Developer Environment

    1. Go to Make.powerapps.com and ensure the selected environment is your Developer Environment (1)

     ![alt text](image.png)

     2. Select Solutions (2) then Import Solution (3)

     3. Browse for the solution you downloaded and select Next

     4. Follow the messages selecting Next each time

     5. This should install the sample solution with 5 tables.

     6. In Solutions again, select the solution we just imported and ensure it plays

     ![alt text](image-1.png)

     Select the solution, then apps and the 3 dots next to the Contoso Real Estate Administration App and Play

     ![alt text](image-2.png)

     The application should load (with no data)

     7. Import some sample data

     Back in the solution, select Tables, Listing, The Import dropdow and select Import data from excel

     ![alt text](image-3.png)

     8. Select the listings file extracted from the data files zip in step 2.

     9. Select Import

     Repeat the last 3 steps for the other files in the zip file and upload data (in this order) for

        1. contoso_listingimages
        2. contoso_listingfeetypes
        3. contoso_listingfees
        4. contacts

    10. Reopen the app from step 6 and you should see data being populated.
