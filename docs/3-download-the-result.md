---
title: Download the Results
parent: Getting Started with the Web of Science PostgreSQL Database - Mac
layout: default
nav_order: 3
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren
maintainer:
    - name: Leslie Barnes
      link: https://library.utoronto.ca/staff/leslie-barnes
created_date: 2022-02-07
---

## Download the Results

1. From the Trillium prompt, type `ls` to list all the files in your personal directory. If you followed the steps above, you should see a csv file you just saved
2. There are multiple ways to download your data file via Terminal, which are outlined in the [SciNet Wiki](https://docs.computecanada.ca/wiki/Transferring_data). If you need to ensure that two datasets remain synchronized, you may want to use rsync or Globus. Otherwise, SFTP or SCP will work well to transfer files back and forth from your local machine to the remove environment.
3. To download using SCP:
	* Open a new Terminal window that is not connected to Trillium (ie. your local directory),  and run the following command: `scp <computecanadausername>@trillium.scinet.utoronto.ca:[filename including extension] /some/local/directory`
		+ For example, to download the files to a SciNet folder in your Documents: `scp doej@trillium.scinet.utoronto.ca:myfirstqueryresult.csv /Users/user/Documents/SciNet`
		+ If prompted, enter your SSH key passphrase
		+ Note: if you received a permission denied error and are not prompted for your passphrase, try adding -i <**privatekeyname**> to the scp command: `scp -i <privatekeyname> <computecanadausername>@trillium.scinet.utoronto.ca:[filename including extension] /some/local/directory`
4. You should see a progress bar, which will show 100% once the download is complete
5. Now if you go to that directory, you should see your new csv file. Open it up and view your results!

**Technique:** [Searching for maps and data](https://mdlutoronto.github.io/tutorials-search/?technique=Searching+for+maps+and+data), [Text and Data Mining](https://mdlutoronto.github.io/tutorials-search/?technique=Text+and+Data+Mining) \| **Tools:** [Web of Science](https://mdlutoronto.github.io/tutorials-search/?tool=Web+of+Science)
