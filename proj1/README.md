
<html><head>
    <title>CS61C Summer 2015 Project 1</title>
    <link rel="stylesheet" type="text/css" href="../style.css">
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>

<div class="header">
    <h1>CS61C Summer 2015 Project 1: <tt>beargit</tt></h1>
    TA: Jeff Wettstein <br>
</div>

<div class="content">
    <p>Due Sunday, July 5, 2015 @ 11:59pm</p>

    <p>
        <ul class="error">
        </ul>
    </p>
    <h2>Goals</h2>
        <ul>
            <li>Learn more about <tt>git</tt> by building a simpler version, called <tt>beargit</tt> (Go Bears!)</li>
            <li>Write a substantial C program</li>
            <li>Learn about branches and checkouts in <tt>git</tt> and add similar functionality to <tt>beargit</tt>.</li>
            <li>Learn to test your C application to aid in building robust programs.</li>
        </ul>

    <h2>What is <tt>beargit</tt>?</h2>
    
    <p><tt>git</tt> is a great tool for managing source code and other files. However, even great tools can be used for evil; what if someone uses it to create <tt>git</tt> commits with hideous messages such as "St****rd RuLEz!"? So in this homework, you will be developing your own version of <tt>git</tt>, which will put an end to such behavior by requiring every commit message to contain the words "GO BEARS!". ;-)</p>

    <p>At its core, <tt>beargit</tt> is essentially a simpler version of <tt>git</tt>, which you should have become familiar with
    in Lab 0. <tt>beargit</tt> can track individual files in the current working directory (no subdirectories!).
    It maintains a <tt>.beargit/</tt> subdirectory containing information about your repository. 
    For each commit that the user makes, a directory is created inside the <tt>.beargit/</tt>
    directory (<tt>.beargit/&lt;ID&gt;</tt>, where &lt;ID&gt; is the ID of the commit). The <tt>.beargit/</tt> directory additionally contains two files: <tt>.index</tt> (a list of all currently
    tracked files, one per line, no duplicates) and <tt>.prev</tt> (contains the ID of the last commit, or
    0..0 if there is no commit yet). Each <tt>.beargit/&lt;ID&gt;</tt> directory contains a copy of each
    tracked file (as well as the <tt>.index</tt> file) at the time of the commit, a <tt>.msg</tt> file that
    contains the commit message (one line) and a <tt>.prev</tt> file that contains the commit ID
    of the previous commit.</p>


    <h2>Key differences between <tt>beargit</tt> and <tt>git</tt>:</h2>

    <ul>
        <li>The only supported commands are <tt>init</tt>, <tt>add</tt>, <tt>rm</tt>, <tt>commit</tt>, <tt>branch</tt>, <tt>checkout</tt>, <tt>status</tt> and <tt>log</tt>. For each of them, only the most basic command line options are supported.
        <li><tt>beargit</tt> does not track diffs between files. Instead, each time you make a commit, it simply copies all files that are being tracked into the <tt>.beargit/&lt;ID&gt;</tt> directory (where <tt>&lt;ID&gt;</tt> is the commit ID).</li>
        <li>Commit IDs are not based on cryptographic hash functions, but instead are a fixed sequence of 40-character strings that only contain '6', '1' and 'c' (why we chose those characters is left as an exercise to the reader).</li>
        <li>Any commits with a commit message that does not contain "<tt>GO BEARS!</tt>" (with exactly this capitalization and spelling) will be rejected with an error message.</li>
        <li>No user, date or other additional information is tracked by <tt>beargit</tt>. It does not allow to track subdirectories, or files starting with <tt>'.'</tt>.</li>
        <li>The <tt>rm</tt> command only causes <tt>beargit</tt> to stop tracking a file, but does not delete it from the file system.</li>
    </ul>
       
        
    <h2>Files:</h2>
    <ul>
        <li><tt>beargit.c</tt> - This is the file that you will fill in with your implementation of <tt>beargit</tt>.</li>
        <li><tt>cunittests.c</tt> - A file where you can define unit tests to test your code.</li>
        <li><tt>beargit.h</tt> - Do not edit - This file contains declarations of various constructs in <tt>beargit.c</tt> along with convenient <tt>#define</tt>s. See the "Important Numbers" section below.</li>
        <li><tt>cunittests.h</tt> - Do not edit - Declarations needed for use with the cunit library.
        <li><tt>main.c</tt> - Do not edit - Contains the main for <tt>beargit</tt> (which parses command line options and calls into the functions defined in <tt>beargit.c</tt>).</li>
        <li><tt>Makefile</tt> - Do not edit - This tells the program <tt>make</tt> how to build your code when you run the <tt>make</tt> command. This is a convenient alternative to having to repeatedly type long commands involving <tt>gcc</tt>.</li>
        <li><tt>util.h</tt> - Do not edit - Contains helper functions declarations you may want to use in this assignment.</li>
        <li><tt>util.c</tt> - Do not edit - Contains implementation of the helper functions declaraed in until.h (you may want to see the specific details of what those functions are doing before you use them) </li>
    </ul>

    <p><strong>You should only modify and submit <tt>beargit.c</tt>. Our autograder will overwrite all other files with a fresh copy.</strong></p>

    <h2>Important Numbers: (see <tt>beargit.h</tt>)</h2>

    <p>In lecture, you learned about using <tt>#define</tt> to define constants as a single source of truth. You should use the appropriate constants from <tt>beargit.h</tt> whenever you are using any of the following numbers:</p>

    <ul>
        <li>Commit ID lengths are limited to 40 characters (not including the null terminator), 10 of which are composed of the branch id.</li>
        <li>Filenames are limited to 512 characters (including null terminator).</li>
        <li>Commit messages are limited to 512 characters in length (including null terminator).</li>
        <li>Branch names are limited to 128 characters (including null terminator).
    </ul>

    <h2>Preliminaries:</h2>
    
    <p>For this homework, you will be using some C functionality that you may not be familiar with. We will now highlight some of these features:</p>

    <h4>C library functions:</h4>
    
    <p>You may wish to familiarize yourself with the following C library functions: <tt>fprintf, sprintf, fopen (and fclose, fwrite, etc.), strcmp, strlen, strtok,</tt> and <tt>fgets</tt>. You can find documentation of the C library <a href="http://www.cplusplus.com/reference/clibrary/">here</a> (use the search box at the top to find out about each function). Make sure not to stray away from the "C library" section, as the linked website also contains C++ documentation.</p>
    
    <p>When you look at the existing code in <tt>beargit.c</tt>, you will see examples of how these functions can be used to achieve the desired functionality. We recommend trying to understand the provided functions first, before starting to implement your own.</p>
   
    <h4>Handling I/O (more than just <tt>printf</tt>):</h4>

    <p>Unix machines use a concept called "streams" to handle arbitrary I/O. We will need two of these output streams in this homework. The first is stdout, which is where your output goes when you call <tt>printf</tt>. We will use stdout to output all output indicating a "successful" action. The other output stream is stderr, which is where we will output all error messages. By default, both of these streams, stdout and stderr, are printed to your screen when you run a program.</p>

    <p>Outputting to either stdout or stderr can be done similarly to using printf. The only change is that you use the <tt>fprintf</tt> function, and the first argument you pass in must be either "stderr" or "stdout" (without quotes).</p>


    <pre>[inside your C code]
fprintf(stdout, "%d\n", 3); // prints the number 3 to stdout, along with a newline
fprintf(stderr, "%d\n", 4); // prints the number 4 to stderr, along with a newline</pre>

    <h4>Included helper functions:</h4>

    <p>To make life easier for you, we provide helper functions for common operations that you will encounter while implementing <tt>beargit</tt>. You fill find these in <tt>utils.h</tt>. Here is a brief overview of each of these functions:</p>

    <ul>
        <li><tt>void fs_mkdir(const char* dirname)</tt>: Create a new directory of name <tt>dirname</tt></li>
        <li><tt>void fs_rm(const char* filename)</tt>: Delete the file <tt>filename</tt></li>
        <li><tt>void fs_mv(const char* src, const char* dst)</tt>: Move the file <tt>src</tt> to <tt>dst</tt>, potentially overwriting it</li>
        <li><tt>void fs_cp(const char* src, const char* dst)</tt>: Copy the file <tt>src</tt> to <tt>dst</tt>, potentially overwriting it</li>
        <li><tt>int fs_check_dir_exists(const char* dirname)</tt>. This function tests whether a given directory exists.</li>
        <li><tt>void write_string_to_file(const char* filename, const char* str)</tt>: Create or overwrite the file <tt>filename</tt> and write <tt>str</tt> into it, including the NULL-character</li>
        <li><tt>void read_string_from_file(const char* filename, char* str, int size)</tt>: Open the file <tt>filename</tt> and read its content into the location pointed to by <tt>str</tt>; limit the amount to read to at most <tt>size</tt> bytes, including the NULL character</li>
 

    </ul>
    
    <p>The last two functions should only be used together. Specifically, <b>don't try to use <tt>read_string_from_file</tt> to read multi-line files</b>, but only for single strings that you previously wrote into a file using <tt>write_string_to_file</tt>.</p>
    
    <br><b>While these functions perform some basic checks to prevent you from accidentally overwriting important files, be careful whenever you call any function that modifies the file system. There is always a risk of unintentionally deleting or overwriting files, especially when working on your own machine!</b>

    <h2>Setup:</h2>

    <p>To get starter code for this homework, we will be using <tt>git</tt>. 
    First, you must enter into your cs61c class repository in your class
    account (this is the repository you created in last week's lab; probably located in <tt>~/work</tt>).
    Once in this directory, run the following:</p>

    <pre>git remote add proj1-starter git@github.com:cs61c-summer2015/proj1-starter
git fetch proj1-starter
git merge proj1-starter/master -m "merge proj1 skeleton code"</pre>

    <p>Once you complete these steps, you will have the <tt>proj1</tt> directory inside of
    your repository, which contains the files you need to begin working.</p>

    <p>As you develop your code, you should make commits in your <tt>git</tt> repo and push them as you 
    see fit. Be sure not to get confused between <tt>beargit</tt>, which you 
    are writing for this homework, and <tt>git</tt>, which is managing your 
    real class repository.</p>


    <h2>Required functionality:</h2>

    <p>While the version of <tt>beargit</tt> that we've given to you compiles, you can't do much except call
    <tt>beargit init</tt> to create a new repository, and call <tt>beargit add &lt;file&gt;</tt> to start
    tracking a file. Everything else you need to implement yourself! </p>

    <p>We recommend that you implement the beargit commands in this order, as this makes testing easier:</p>
    <ol>
        <li><tt>beargit status</tt></li>
        <li><tt>beargit rm</tt></li>
        <li><tt>beargit commit</tt></li>
        <li><tt>beargit log</tt></li>
        <li><tt>beargit branch</tt></li>
        <li><tt>beargit checkout</tt></li>
    </ol>
    
    <p>For each of these, you need to implement one of the functions below (but feel
 free to define new helper functions to make things easier). We give you an
 outline of each function's job, as well as the errors you need to be able to
 detect, and the output you need to produce.</p>

    <p><b>Note: Whenever you see the notation <tt>&lt;something&gt;</tt>, you should replace it with the appropriate value for <i>something</i>, without the angle brackets</b></p>
    
    <h2>Testing your code in CS61C</h2>
        
    <p>Unlike CS classes you may have taken in the past, we will not provide you with a full 
    autograder for the assignment. Instead, you should devise a methodology to
    test your code to ensure that it performs as you intend it to. The autograder
    that produces your final grade will include many more test cases than the
    autograder/sanity check provided with the homework.</p>

    <h4>But... why?</h4>

    <p>When you write production code in the "real" world (and upper division 
    classes), much of the time you will not be provided with any test cases to
    validate your code against (not even a sanity check). The ability to write
    good test cases is just as important a skill for a programmer as the ability to write
    functioning code.</p>

    <p>The test cases you write for this homework won't be submitted or graded, but we may ask
    you to submit test cases for future assignments.</p>
    
    <h3>Automated basic tests</h3>
    
    <p>To make life a bit easier for you, we are providing you with three ways to test your implementation. The first one is an automated testing tool that will run your implementation against a series of basic tests to determine whether its output is sensible. Note that this is just a small subset of the tests that the actual autograder will be running. Even if your program passes all these tests, it may still fail on some of the test cases in the autograder. You therefore shouldn't rely on this tool for your testing but consider it a sanity check.</p>
    
    <p> To run these tests, go into the main source directory and type <tt>make check</tt>. You will see output similar to the following:</p>
        
    <pre>Running test cases...

  [  OK  ] beargit_test_add_0
  [  OK  ] beargit_test_add_1
  [  OK  ] beargit_test_rm_0
  [ FAIL ] beargit_test_rm_1 file is missing but no (or incorrect) error message (error type: OUTPUT)
  [ FAIL ] beargit_test_status_0 expected 4 lines of output but found 0 (error type: OUTPUT)
  [ FAIL ] beargit_test_status_1 expected 2 lines of output but found 0 (error type: OUTPUT)
  [  OK  ] beargit_test_commit_0
  [ FAIL ] beargit_test_commit_1 successful commit should not display any output (error type: OUTPUT)
  [ FAIL ] beargit_test_log_0 there are no commits, but no correct error message was shown (error type: OUTPUT)
  [ FAIL ] beargit_test_log_1 there are no commits, but no correct error message was shown (error type: OUTPUT)

TESTS PASSING: 4 / 10</pre>

    <p>You should pay close attention to the error messages, as they are designed to give you a hint what is going wrong.</p>
    
    <h3>Manual testing</h3>
    
    <p>For your own interactive testing, we provide you with a script that creates a new test directory for you (called <tt>test</tt>), which you can use to experiment with your implementation in a fresh directory (where it will generate the <tt>.beargit/*</tt> files and directories). Every time you use the script, your previous directory will be deleted, so you can start afresh. <b>Be careful to not leave any important data in the test directory!</b></p>
    
    <p>To run the script and create a new test directory, run the following in your <tt>proj1</tt> directory (this will automatically move you into the test directory and add your beargit executable to the PATH, so that you can run it):
        
    <pre>$ source init_test</pre>
    
    <p>You can then run commands such as:</p>
    
    <pre>$ beargit init
$ touch test.txt
$ beargit add test.txt</pre>

    <h2>Unit testing in C </h2>

    <p>In order to make automated testing easier for you, we've hooked up a framework called
    CUnit to the beargit code. You can learn more about CUnit 
    <a href="http://cunit.sourceforge.net/doc/index.html">here</a>.</p>

    <p>One issue with Unit Testing is that by default you wouldn't be able to 
    capture the output of calls to printf or fprintf (to stdout and stderr). 
    Since your outputs to stdout/stderr are important for beargit, we've provided
    some code that replaces calls to printf/fprintf with a custom function that
    directs output to two files, <tt>TEST_STDOUT</tt> and <tt>TEST_STDERR</tt>.
    You can read these files as you would any other file, allowing your testing
    code to analyze the output that your functions print to the screen.</p>

    <p>All of your unit tests will live in <tt>cunittests.c</tt>. We've provided
    two example test suites, each containing one test, along with test suite
    initialization functions to make your life easier. The initialization function
    (<tt>int init_suite(void)</tt> in <tt>cunittests.c</tt>) will destroy any existing .beargit directory, and remove old copies of 
    <tt>TEST_STDOUT</tt> and <tt>TEST_STDERR</tt>. You will most likely not
    need to use the <tt>clean_suite</tt> function, which runs at the end of a
    test suite, but we have provided the stub in case you need it.</p>

    <h3>What is a "suite"?</h3>
    <p>A test suite is essentially a collection of tests. To add test suites, 
    you can use the following boilerplate code in <tt>cunittests.c</tt>. Two
    examples of this are already provided.</p>


    <h4>Creating the Test Suite</h4>

    <p> You'll need to add the following code in <tt>cunittester()</tt> once per test suite:</p>
    <pre>
[... inside of cunittester() in cunittests.c  ...]
[ Replace pSuiteN below with a suitable name, like pSuite5 ]

CU_pSuite pSuiteN = NULL; // replace N with the test number
/* add a suite to the registry */

/* You don't necessarily have to use the same init and clean functions for
 * each suite. You can specify the function names in the next line:
 */
pSuiteN = CU_add_suite("Suite_N", init_suite, clean_suite);
if (NULL == pSuiteN) {
   CU_cleanup_registry();
   return CU_get_error();
}</pre>

    <h4>Adding Tests to the Suite</h4>

    <p> You'll need to add the lines below for each test function that you want
    to add to the suite. In the example below, we are adding the function 
    <tt>simple_sample_test</tt> to the suite.</p>

    <pre>
[... also inside of cunittester() in cunittests.c ...]
/* Add test named simple_sample_test to Suite #N */
if (NULL == CU_add_test(pSuiteN, "Simple Test #N", simple_sample_test))
{
   CU_cleanup_registry();
   return CU_get_error();
}</pre>

    <h4>How Tests Are Run</h4>

    <p>CUnit performs the following actions when running a test suite:

    <ol>
        <li>Runs the suite initialization function. In the above code, this 
        function is called <tt>init_suite</tt>.</li>
        <li>Runs all of the tests you added to the suite. In the above example,
        this runs only the function named <tt>simple_sample_test</tt>.</li>
        <li>Runs the suite cleanup function. In the above code, this function
        is called <tt>clean_suite</tt>.</li>
    </ol>

    The initialization function is useful, because you can use it to automatically
    destroy any existing <tt>.beargit</tt> directory before your tests run, so that
    you can create a new repo with <tt>beargit init</tt>. In the code we have
    given you, we do not destroy files in the cleanup function (the function is
    actually empty). This allows you to peek into the <tt>.beargit</tt> directory and
    the <tt>TEST_STDOUT</tt> and <tt>TEST_STDERR</tt> files in case you need to do 
    so manually.</p>

    If you want to get started on testing right away, please skip ahead to Step 8 to see how you can run the tests for your <tt>beargit</tt> implementation. If you prefer to get started on finishing <tt>beargit</tt> first, please read on.


<h2>String manipulation tips and warnings</h2>
<p>For a large portion of this project you will be dealing with manipulating strings. You can use this section as a reference if you are running into trouble</p>
<h3>Concatinating two strings</h3>
    <p>There are two functions you can use: <tt>strcat()</tt> and <tt>sprintf()</tt>. </p>
    <h4>strcat(char * dst, char *src)</h4>
    <p>Note that <tt>strcat(".beargit/","test")</tt> is incorrect. ".beargit/" is a string literal which is of fixed size thus you cannot safely append test to the end of it. Instead you would want to do something of the sort:
<pre>char file[SIZE] = ".beargit/"; 
strcat(file, "test"); // Assuming size >= 14, file points to the string ".beargit/test"</pre> </p>
    <h4>sprintf(char * dst, char * format_string, ...)</h4>
    <p>This works exactly like printf() except that the resulting string is written into dst</p>

<h3>Be careful of string literals!</h3>
<p><pre>char * str = "beargit/"; 
beargit[0] = 'a'; 
... </pre></p>
<p>You may be tempted to do something like this, however it will produce a runtime error. The reason is that str points to a string literal, and string literals are stored in a read-only section. Thus if you want to be able to append to a string that you predefine in this way you must declare str as a char array. This case of declaration and initialization is one of the few cases where there is a difference between arrays and pointers.</p>
<pre>char str[] = "beargit/"; 
beargit[0] = 'a'; 
... </pre>
<p>In this case the string literal is still stored in read-only memory but the character array str is allocated on the stack and recieves a copy of each character in the literal. Thus since str points to a character array on the stack it is modifiable.</p>

<h3>How can I remove newlines from strings when I'm reading in files</h3>
<p>The function strtok() is going to help you accomplish this. Since you will be reading in a few single-line files this can be used to remove the newline from the end of the string.</p>
<pre> char * str = "...\n";
strtok(str, "\n"); </pre>
<p>All you need to know is this function is replacing the '\n' character with a NULL terminator thus effectively removing it from the end of your string. More information can be found in strtok() documentation if you are curious.


    <h3>Step 1: The <tt>status</tt> command</h3>

    <h4>Functionality:</h4>

    <p>The status command in <tt>beargit</tt> should read the file 
    <tt>.beargit/.index</tt> and print a line for each tracked file. The 
    exact format is described below. Unlike <tt>git status</tt>, <tt>beargit status</tt> should not print anything about untracked files.</p> 

    <h4>Output to stdout:</h4>

    <pre>$ beargit status
Tracked files:

 &lt;file1&gt;
 [...]
 &lt;fileN&gt;

&lt;N&gt; files total</pre>

    <p>For each file in the above output, <tt>&lt;file*&gt;</tt> should be replaced with the filename of that file.</p>

    <h4>Return value and output to stderr:</h4>

    <p>This function should always return 0 (indicating success) and should never output to stderr.</p>


    <h3>Step 2: The <tt>rm</tt> command</h3>
    
    <p><i>Hint: You may want to have a look at the provided implementation of <tt>beargit add</tt> before implementing this command.</i></p>
 
    <h4>Functionality:</h4>
       
    <p>The rm command in <tt>beargit</tt> takes in a single argument, which specifies
    the file to remove from the index (which is stored in the file <tt>.beargit/.index</tt>). If the filename
    passed in is not currently being tracked, you should print an error as indicated
    below. Note that this behavior is different from <tt>git</tt> in that it doesn't delete the file from your file system.</p>

    <h4>Output to stdout:</h4>
    <p>None.</p>

    <h4>Return value and output to stderr:</h4>

    <p>If the filename specified in the provided argument exists in the index, 
    the function should return 0 and produce no output on stderr. If the filename
    specified does not exist in the index, the function should return 1 and output
    the following to stderr:</p>

    <pre>$ beargit rm FILE_THAT_IS_NOT_TRACKED.txt
ERROR: File &lt;filename&gt; not tracked</pre>


    <h3>Step 3: The <tt>commit</tt> command</h3>
 
    <h4>Functionality:</h4>
       
    <p>The commit command involves a couple of steps:</p> 

    <ul>
        <li>First, check whether the commit string contains "GO BEARS!". If not, display an error message.</li>
        <li>Read the ID of the previous last commit from <tt>.beargit/.prev</tt></li>
        <li>Generate the next ID (<tt>newid</tt>) in such a way that:
            <ol> 
                <li>ID Length is COMMIT_ID_BYTES (not including NULL terminator)
                <li>All characters of the id are either 6, 1 or c 
                <li>Generating 100 IDs in a row will generate 100 IDs that are all unique (<i>Hint: you can do this in such a way that you go through all possible IDs before you repeat yourself. Some of the ideas from number representation may help you!</i>)</li>
                <li>Calling next_commit_id(char* commit_id) results in commit_id being updated to a ID.
                <li>The ID string consists of a branch-id (of size COMMIT_ID_BRANCH_BYTES) followed by a tag-id to fill the rest of the size of the ID. (Note: the tag-id used here has nothing to do with a git tag, git tags aren't involved in this project!)
                <li>We have implemented the branch-id step for you in next_commit_id(char* commit_id). Don't worry too much about where the branch-id is coming from yet (more on that in part 5), but pay close attention to what indicies in the commit_id string are being updated and how the pointer is being passed to next_commit_id_tag(). To finish the next ID generation you will need to complete next_commit_id_tag().
            </ol>
            </li>
        <li>Generate a new directory <tt>.beargit/&lt;newid&gt;</tt> and copy <tt>.beargit/.index</tt>, <tt>.beargit/.prev</tt> and all tracked files into the directory.</li>
        <li>Store the commit message (&lt;msg&gt;) into <tt>.beargit/&lt;newid&gt;/.msg</tt></li>
        <li>Write the new ID into <tt>.beargit/.prev.</tt></li>
    </ul>
    
    <h4>IMPORTANT RULE THAT WILL AFFECT YOUR GRADE IF YOU DON'T READ IT!</h4>
    
    Now that we have your attention: when implementing the code that checks whether the commit message includes <tt>GO BEARS!</tt>, you are <b>not allowed</b> to use any library functions, including any of the <tt>str*</tt> ones you may have seen before.

    <p><b> NOTE:</b>  <i>beargit -m "GO BEARS!"</i> will result in an error because '!' is a special character in many shells, to avoid this issue use single quotes beargit -m 'GO BEARS!'  </p>

    <h4>Output to stdout:</h4>
    <p>None.</p>

     <h4>Return value and output to stderr:</h4>

     <p>If the commit message does not contain the exact string "GO BEARS!", then you 
     must output the following to stderr and return 1:</p>

    <pre>$ beargit commit -m "G-O- -B-E-A-R-S-!"
ERROR: Message must contain "GO BEARS!"</pre>

    <p>If the commit message does contain the string "GO BEARS!", then the function should
    produce no output and return 0.</p>


    <h3>Step 4: The <tt>log</tt> command</h3>
 
    <h4>Functionality:</h4>
       
    <p>The goal of the log command is to print out either all or a specified number of recent commits. See below for the individual steps:</p> 

    <ul>
        <li>List all commits, latest to oldest. <tt>.beargit/.prev</tt> contains the ID of the latest
        commit, and each directory <tt>.beargit/<ID></tt> contains a <tt>.prev</tt> file pointing to that
        commit's predecessor.</li>
        <li>For each commit, print the commit's ID followed by the commit message (see below
        for the exact format).</li>
        <li>If you pass in the -n flag (e.g. beargit -n 10), then limit the number of log records printed to the amount specified. If the -n flag is not passed, then the argument "int limit" will be set to INT_MAX.
    </ul>

    <h4>Output to stdout:</h4>

    <pre>$ beargit log
[BLANK LINE]
commit &lt;ID1&gt;
    &lt;msg1&gt;
[BLANK LINE]
commit &lt;ID2&gt;
    &lt;msg2&gt;
[...]
commit &lt;IDN&gt;
    &lt;msgN&gt;
[BLANK LINE]</pre>

<p><b>Note:</b> In order to pass our tests you must have exactly the same spacing as above!</p>

    <h4>Return value and output to stderr:</h4>

    <p>If there are no commits to the beargit repo, beargit should return 1 and
    output the following to stderr:</p>
    
    <pre>[assume that no commits have been made]
$ beargit log
ERROR: There are no commits!</pre>

    <p>If there are commits, you should produce the output indicated in the "Output to stdout" section above and return 0.</p>


<h2>How branches and checkouts work in git</h2>

<p>You can go to any commit in the history of time if you know its ID. This is called "checking out a commit". The current state of the working directory will be completely restored to how it was during the time of that commit.</p>

    <p>Branches in git are basically just diverging commit histories. You have an "alternate history" depending on which branch you are on. One way to think about branches is that they allow multiple commits to point to the same previous commit: two branches can have a shared history, and then at some point they do different things starting from a certain point in time.</p>
    
    <p>So every commit has a predecessor, but multiple commits can actually have the same predecessor. In fact, branches themselves are just identifiers for specific commits (which are called the "HEAD" of a branch). Just like commits, you can also check out a branch: in that case, you switch to that branch's HEAD commit. You can also check out commits that are not the HEAD of any branch -- in that case, you say you are "detached", because you are not on any specific branch.</p>
    
    <p>To add branches in <tt>beargit</tt>, not much changes: every commit still has exactly one predecessor (.prev), but multiple commits can have the same predecessor now. Branches in beargit are just pointers to specific commits. To keep things simple, we only allow beargit to commit when you are at the HEAD of a branch (i.e., when you are not detached). This allows you to "grow" each branch forwards.</p>
        
    <p>When you are at any commit, you can start a new branch from there: you can say <tt>git checkout -b &lt;new_branchname&gt;</tt> to start a new branch that has the current commit as its HEAD. You can then start an alternative history by committing on this branch. When you initialize a new beargit repository, a default branch <tt>master</tt> is created, and its HEAD points to the 00.0 commit ID.</p>

    <h3>Visualizing Branches</h3>

    <p>To help you get a better sense of how branches actually work, you should work through 
    the following tutorial until you are satisfied that you understand what branches do: 
    <a href="http://pcottle.github.io/learnGitBranching/">http://pcottle.github.io/learnGitBranching/</a>.
    </p>

    
    <h2>Required functionality:</h2>
    
    <p>While implementing branches may sound very complicated, it is not much additional work to what you have already implemented -- in your last homework, you have created a solid foundations to build upon, so now things get easier.</p>
    
    <h4>Directory structure</h4>

    <p>We will implement branches very similarly to how we implemented tracking of files. All we have to do is add a few files to our directory structure:</p>
    <ul>
        <li><tt>.beargit/.branches</tt> is a file that contains a line for every branch that exists. We will call the line number on which the branch exists in this file the "branch number" (starting from 0).</li>
        <li><tt>.beargit/.current_branch</tt> contains a single string with the name of the current branch if we are at the HEAD of some branch, or is an empty string if we are not on some branch HEAD.</li>
        <li><tt>.beargit/.branch_&lt;branchname&gt;</tt> (one for every branch). This is a copy of the .prev file that belongs to the branch head (i.e., the HEAD commit of the branch)</li>
    </ul>
    
    <p>With this information, we can now implement beargit branch and beargit checkout.</p>
    
    <h3>Step 5: The <tt>branch</tt> command</h3>
 
    <h4>Functionality:</h4>
       
    <p>Beargit branch prints all the branches and puts a star in front of the current one. Do you remember beargit status? This is almost the same: you need to read the entire .branches file line by line and output it. However, you also need to check each line against the string in .current_branch. If they are the same, you need to print a * in front of it.</p>
    
    <p>Note that we require you to print branches in the order of creation, from oldest to latest. Also note that if you have checked out a commit previously (in contrast to a branch), you are detached from the HEAD and don't have to print a star in front of any branch. This is even true if the commit you checked out is actually the HEAD of a branch.

    <h4>Output to stdout:</h4>
    
    <pre>$ beargit branch
  &lt;branch1&gt;
  &lt;branch2&gt;
  [...]
* &lt;current_branch&gt;
  [...]
  &lt;branchN&gt;</pre>

    <h4>Return value and output to stderr:</h4>

    <p>This function should always return 0 (indicating success) and should never output to stderr.</p>
    
    <h3>Step 6: The <tt>checkout</tt> command</h3>
        <h4>Functionality:</h4>
    
    <p>This is the command that is the most important feature of beargit. It allows you to restore the state of any commit in time, as well as to switch and create branches. beargit checkout has three different behaviors:</p>
    <ul>
        <li><tt>beargit checkout &lt;commit_id&gt;</tt>: Check out a particular commit (i.e., leaving a branch HEAD if you are on it; this is called a "detached" state. You can assume that whenever you call this, you become detached, even if the commit you are checking out is some commit's HEAD).</li>
        <li><tt>beargit checkout &lt;branch&gt;</tt>: Check out an existing branch and check out its head.</li>
        <li><tt>beargit checkout -b &lt;newbranch&gt;</tt>: Start a new branch at the current commit.</li>
    </ul>
    
    <p>While these behaviors look very different, they are actually very similar. First, you need to find out which of the three cases it is. We give you whether the user has provided <tt>-b</tt> (the new_branch bool parameter) and then the other argument, which can be either a commit ID or a branch name.</tt></p>
    
    <p>So beargit first needs to find out if you are giving it a commit or a branch name. For this, we have prepared a function <tt>is_it_a_commit_id</tt>, which you need to fill in. The function takes a string and returns true if and only if the string is 40 characters that are each 6, 1 or c.</p>
    
    <p>Once you know whether you are dealing with a branch or a commit, you have to do one of two things:
        <ol>
            <li>If it's a commit, check out the commit by replacing the currently tracked files with those from the time of the commit.</li>
            <li>If it's a branch (and you're not creating a new one), first check whether it exists. If yes, you need to switch to that branch. This means that you first store the latest commit of the current branch into the branch_branchname file, and then replace the content of current_branch by the new branch. You then read the branch_newbranch file to find out the HEAD commit of that branch, and then you check that commit out just like in 1).</li>
            <li>You are creating a new branch. This is very similar to 2), but you also have to add the branch to the .branches file and instead of reading the HEAD ID from .branch_branchname, you make the current prev ID the head ID for that branch and store it into that file.</li>
        </ol>
        
        <p>Since we are nice people, we actually implemented the functionality above for you, except for the implementation of the actual checkout! But because we had to write this homework in a rush, there are three mistakes in the beargit_checkout function -- you need to find and correct them for everything to run (one line per mistake). Consider using cgdb and printf for debugging to help you!</p>
        
        <p><i>Note: The <tt>beargit_checkout</tt> function is taking two arguments: <tt>new_branch</tt> is true if and only if <tt>-b</tt> was supplied to the command, and <tt>arg</tt> contains the other command line argument.</i></p>

        <p>After you found the mistakes, you have to write a function checkout_commit which will do the actual checkout of a commit by:</p>
        <ul>
            <li>Going through the index of the current index file, delete all those files (in the current directory; i.e., the directory where we ran beargit).</li>
            <li>Copy the index from the commit that is being checked out to the <tt>.beargit</tt> directory, and use it to copy all that commit's tracked files from the commit's directory into the current directory.</li>
            <li>Write the ID of the commit that is being checked out into .prev.</li>
            <li>In the special case that the new commit is the 00.0 commit, there are no files to copy and there is no index. Instead empty the index (but still write the ID into .prev and delete the current index files). You may wonder how we could ever check out the 00.0 commit, since it is not a valid commit ID; the answer is that if you check out a branch whose HEAD is the 00.0 commit, that checkout is expected to work (while 00.0 would not be recognized as a commit ID).</li>
        </ul>
        
        <p>Once you are done, you should experiment with the checkout and branch functionality by creating new branches, checking out old commits and see how you can commit to different branches individually. There is a lot that can go wrong, so we recommend testing thoroughly, and writing CUnit tests.</p>



        <h4>Output to stdout:</h4>
    
    None.

        <h4>Return value and output to stderr:</h4>
        
        <p>If the argument is a commit ID (40 characters, each of which is '6', '1' or 'c') of a commit that exists, a branch that exists and <tt>new_branch</tt> is false, or a branch that doesn't exist and new_branch is true, the function should return 0 and produce no output on stderr.</p>
        
        <p>If the argument is a commit ID but the commit does not exist, the function should return 1 and produce the following error:</p>
        
        <pre>$ beargit checkout 6666.66
ERROR: Commit &lt;commit_id&gt; does not exist</pre>
        
        <p>If the argument is a branch that exists but <tt>new_branch</tt> is true, the function should return 1 and produce the following error:</p>

        <pre>$ beargit checkout -b &lt;branch_name&gt;
ERROR: A branch named &lt;branch_name&gt; already exists</pre>
        
        <p>If the argument is a branch that does not exist but <tt>new_branch</tt> is false, the function should return 1 and produce the following error:</p>

        <pre>$ beargit checkout &lt;branch_name&gt;
ERROR: No branch &lt;branch_name&gt; exists</pre>

    <h3>Step 7: Testing</h3>
    
    <p>As the final part of this assignment, you will need to write 2 test suites
    that each focus on a different beargit command. Each of the two test 
    suites must have a comment at the top describing what beargit command the 
    suite is designed to test and the kinds of error conditions the test will
    catch. You will write these in 
    <tt>cunittests.c</tt>. This file will be turned in and a reader will look
    over your test code to ensure that your tests are reasonable.</p>

    <p>We've also provided a linked list structure called <tt>commit</tt> inside 
    of cunittests.c, which you may find helpful in programmatically keeping 
    track of a sequence of commits in your test code. An example of its usage
    is found in <tt>simple_log_test</tt>.</p>

    <p>Although you are only required to turn in 2 tests, it is highly 
    recommended that you write additional tests to ensure that your 
    implementation works as expected.</p>

    <h4>Running Tests</h4>
    <p>In order to run tests, you should do the following:</p>

<pre>[assumes you are inside your proj1 directory]
$ make beargit-unittest
$ source init_test
$ beargit-unittest     


     CUnit - A unit testing framework for C - Version 2.1-3
     http://cunit.sourceforge.net/

rm: cannot remove '.beargit': No such file or directory &lt;- You can ignore this

Suite: Suite_1
  Test: Simple Test #1 ...passed
Suite: Suite_2
  Test: Log output test ...passed

Run Summary:    Type  Total    Ran Passed Failed Inactive
              suites      2      2    n/a      0        0
               tests      2      2      2      0        0
             asserts      4      4      4      0      n/a

Elapsed time =    0.007 seconds</pre>


    <h2>Submission</h2>
        
    <p>There are <strong>two</strong> steps required to submit proj1. Failure to perform both steps will result in loss of credit:</p>

    <ol>
        <li><p>First, you must submit using the standard unix submit program on the instructional servers. This assumes that you followed the earlier instructions and did all of your work inside of your <tt>git</tt> repository. To submit, follow these instructions after logging into your cs61c-XX class account:</p>

            <pre>$ cd ~/work                                              # your git repo, should contain a directory called proj1 with your soln
$ cd proj1
$ submit proj1</pre>
            <p> Once you type <tt>submit proj1</tt>, follow the prompts generated by the submission system. It will tell you when your submission has been successful and you can confirm this by looking at the output of <tt>glookup -t</tt>.</p>
            <br>
        </li>

        <li><p>Additionally, you must submit proj1 to your GitHub repository. To do so, follow these instructions after logging into your cs61c-XX class account:</p>

        <pre>$ cd ~/work                                              # your git repo, should contain a directory called proj1 with your soln
$ git add -u                                             # should add all modified files in proj1 directory (must include beargit.c)
$ git commit -m "Project 1 submission"
$ git tag -f "proj1-sub"                                   # The tag MUST be "proj1-sub". Failure to do so will result in loss of credit.
$ git push origin proj1 --tags                             # Note the "--tags" at the end. This pushes tags to github</pre>
            </li>

    </ol>

    <h4>Resubmitting</h4>
    <p>If you need to re-submit, you can follow the same set of steps that you would 
    if you were submitting for the first time. The only exception to this is in the very last
    step, <tt>git push origin proj1 --tags</tt>, where you may get an error like the following:</p>


    <pre>(21:28:08 Sun Feb 01 2015 cs61c-ta@hive12 Linux x86_64)
~/work $ git push origin proj1 --tags
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (19/19), done.
Writing objects: 100% (21/21), 9.73 KiB | 0 bytes/s, done.
Total 21 (delta 4), reused 0 (delta 0)
To git@github.com:cs61c-summer2015/cs61c-ta
   bf20433..d1ff9ed  proj1 -> proj1
 ! [rejected]        proj1-sub -> proj1-sub (already exists)
error: failed to push some refs to 'git@github.com:cs61c-summer2015/cs61c-ta'
hint: Updates were rejected because the tag already exists in the remote.</pre>

<p>If this occurs, simply run the following instead of <tt>git push origin proj1 --tags</tt>:</p>

<pre>$ git push -f origin proj1 --tags</pre>


<p>Note that in general, force pushes should be used with caution. They will overwrite
your remote repository with information from your local copy. As long as you have
not damaged your local copy in any way, this will be fine.</p>

</div>
</body>
</html>