# Pre-launch considerations
Naive use of HPC resources can easily lead to mistakes that disrupt other users. Hence, we ask that you please go over the user manual, guides, phrases and terms, and FAQ to make sure you understand the concepts therein before you run any code on LUNARC resources. [The HPC Wiki](https://hpc-wiki.info/hpc/Category:Basics) is also a good resource if you want a more thorough explanation of [commonly used phrases and terms](https://lunarc-documentation.readthedocs.io/en/latest/manual/manual_common_phrases/) in the manual.

### Don't clog the login node!
The most disruptive mistake that the LUNARC support team must regularly fix is users logged into the HPC system running code directly as if it were on their own laptops. **Be careful what you run outside the SLURM scheduling system!** If you run a resource-intensive job directly, either by calling the code file at the command line or through a user interface (JupyterLab for example), it will run on the frontend - the login node - creating a bottleneck that can stall other users' jobs. *You must create a job script and run your code by calling the job script with sbatch*. For more on job scripts, [refer to the section of the manual on submitting jobs](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_basic_job/).

Remember that repeated misuse of the frontend in this way is a violation of the terms and conditions of your SUPR user account, and may result in your HPC access being revoked.

## Training through NAISS
LUNARC and NAISS offer a variety of training and development courses, including several recurring workshops targetting users new to high performance computing. We highly recommend that all new users check on SUPR that they are signed up for the NAISS Training Newsletter. To check your subscription status, log into SUPR and click the Groups tab. If you are signed up for the newletter (which typically happens automatically upon creating an account), it will appear under the heading "Group You Manage or Belong To". Otherwise, you should be able to find "NAISS-training-subscribe" under the "Groups You Can Join" heading.

You can also find the list of upcoming events on the [LUNARC website under "Learning More".](https://www.lunarc.lu.se/learning-more/training-courses/)



