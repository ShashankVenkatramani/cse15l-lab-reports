# Lab Report 5 Week 10

### Finding Results
In order to get the results I copied the MarkdownParse.java from the other repo into a new file called OtherMarkdownParse.java, so I could work with both within the same repository. In order to run the code for all files I used a bash script:

```
for file in test-files/*.md;
do
    java $1 $file
done
```

I first recorded my outputs by running the following command, and stored it in the file myresults.txt:

`bash script.sh MarkdownParse > myresults.txt`

I then recorded the other outputs by running the following command, and stored it in the file otherresults.txt:

`bash script.sh OtherMarkdownParse > otherresults.txt`

From there I used `diff` to find the difference between my results and the other results, and manually searched the terminal output.

`diff myresults.txt otherresults.txt`

From there I get output for differences such as:

```
< {test-files/611.md=[foo@bar.example.com, foo@bar.example.com]}
---
> {test-files/611.md=[]}
```

### Test one (611.md)

My implementation is incorrect here in multiple ways. The input file is as follows:
```
foo@bar.example.com
```
and it should output no links, which the other program does with the output: `[]`

My implementation however outputs: `[foo@bar.example.com, foo@bar.example.com]`

Although no links should be showing, the fact that it duplicated a single link is indicative of another bug, which is that links with several periods get reported multiple times (one for each period). In this case, the problem is that the program goes through character by character, searches for periods, and then expands outwards until a stop character is hit on both ends. From there it starts searching from the character *after the period*.

This is problematic since after it finds the first period after "bar" it will hit the period that is after example, and expand outwards again etc, and report the same link as seen above. The code responsible for this is:

```
while(currentIndex < markdown.length()) {
    int nextPeriodIndex = markdown.indexOf(".", currentIndex + 1);
    if(nextPeriodIndex != -1) {
        periodList.add(nextPeriodIndex);
        currentIndex = nextPeriodIndex;
    } else {
        break;
    }
}
for(int periodIndex: periodList) {
    //...parsing code here...
}
```

It builds the period list first and then iterates through the periods. A way to fix this would be to have the entire parsing link from period for loop, be inside the while loop instead each time a next period is found. From there once a link/period is found and is added to the list, currentIndex should search for the next period, from the end of the previous link (instead of right after the period). This would stop the multi link reporting bug.

### Test two (579.md)

diff:

```
533c533
< {test-files/579.md=[]}
---
> {test-files/579.md=[<url>]}
```

In this case my implementation is correct, and the other implementation is incorrect.

```
![foo](<url>)
```

and it should output no links, which my program does with the output: `[]`

The other implementation however outputs: `[<url>]`

The bug that shows here is that there isn't a check done for what is before the [name of link... to see if there is an exclamation mark (which would indicate it is an image and not a link).

```
// find the next [, then find the ], then find the (, then take up to
// the next )
int currentIndex = 0;
while(currentIndex < markdown.length()) {
    int nextOpenBracket = markdown.indexOf("[", currentIndex);
    int nextCodeBlock = markdown.indexOf("\n```");
```

In this section of code, when the openbracket is found they should check if a previous character exists on the same line, and if it is an "!", as if it is then we are looking at a markdown image and not a link, and therefore should ignore it.
