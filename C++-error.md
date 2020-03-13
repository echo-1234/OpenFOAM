`wmake`\
__error message__\
`make: warning: Clock skew detected. Your build may be incomplete.`\

__solve__:
You can use the `touch` command for any number of files in the following manner:
1. Just 'cd' into the directory where the files need a time-stamp update.
2. Next use the following command which will update the time-stamps of all the files in the   directory:
   `# find . -exec touch {} \;`
