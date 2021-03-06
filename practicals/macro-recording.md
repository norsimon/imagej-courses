# Batch analysis using IJ macro programming

References:
- https://imagej.nih.gov/ij/developer/macro/functions.html
- https://imagej.nih.gov/ij/developer/macro/macros.html

## Automated counting of all cells in an image stack using a macro

As our first real example of a macro we will automatically count the number of cells in an image.

Turn on macro recorder:

- [Plugins > Macros > Record]

Record workflow for cell counting:

- Open the first time-point of the time-series:
	- [ File > Open ] 
		- '../mitocheck_movie/EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif'
- Threshold
	- [Image > Adjust Threshold]
		- [X] Dark background
		- [Apply]
- Set measurements, keeping tracking of the file-name
	- [Analyze > Set measurements]
		- [X] Area
		- [X] Display label
			- this will keep track of the filename
- Find and measure the cells
	- [Analyze > Analyze Particles...]
		- [X] Summarize
			- This will give you for a given image the average results of all objects
    - Show: Nothing

Your recorded macro should look somewhat similar to the text shown below:

```
open("H:\\imagej-courses-master\\data\\mitocheck-movie\\EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif");
run("Set Measurements...", "area display redirect=None decimal=3");
setAutoThreshold("Default dark");
//run("Threshold...");
setThreshold(18, 255);
setOption("BlackBackground", false);
run("Convert to Mask");
run("Analyze Particles...", "  show=Outlines summarize");
```

- "//" means that a line of code only is a comment
- We have to remove the "//" before the line starting with 'setTreshold', because we actually want to execute it.

If you now [Create] the macro, save it [ File > Save as .. ] and [Run] it, it should do the job.

### Cleaning up and adding comments

In fact there a number of lines recorded that are not really needed, below code is sufficient.

In addition, it is good style to add comments '//' and visually separate code belonging together.

```
// close all images
run("Close All"); 

// open file
open("C:/Users/teach/Desktop/imagej-courses-master/data/mitocheck-movie/EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif");

// threshold
setThreshold(18, 255);
run("Convert to Mask");

// configure measurements
run("Set Measurements...", "area display redirect=None decimal=3");

// perform particle analysis
run("Analyze Particles...", "  show=Nothing summarize");
```

### Activity: Automatically save the results table

Above code does not automatically save the results table, try to add this, using macro recording.

&nbsp;

&nbsp;

#### Solution:

```
saveAs("Results", "C:\\Users\\teach\\Desktop\\Summary.csv");
```

## Using variables

Some commands in our macro will be the same, but some stuff will be different for different files.
It is good style to put all the things that can change at the top of the code, such that it is easy to modify. For this we need so-called "variables".

**=> Interactive practical on variables: numbers, strings, adding, concatenating.**

### Activity: Making filepath and threshold variables

In below code the directory with the images is already a variable (note how string-concatentation was used to paste it into the command).
 
Copy the code into Fiji and also **try to make the threshold a variable**.

```
// user input
filepath = "C:/Users/teach/Desktop/imagej-courses-master/data/mitocheck-movie/EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif";

// close all images
run("Close All"); 

// open file
open( filepath );

// threshold
setThreshold( 18, 255 );
run( "Convert to Mask" );

// configure measurements
run("Set Measurements...", "area display redirect=None decimal=3");

// perform particle analysis
run("Analyze Particles...", "  show=Nothing summarize");

// save results table
saveAs("Results", "C:\\Users\\teach\\Desktop\\Summary.csv");

```

&nbsp;

&nbsp;

#### Solution

```
threshold = 29;
...
setThreshold( threshold, 255 );
```

### Activity: Naming results table as input image

Try to make the filename of the results table resemble the image name, using "String concantenation".

Hints:
- image_filename = "image.tif";
- table_filename = image_filename + ".csv";

#### Solution

```
table_filepath = filepath + ".csv";
saveAs( "Results", table_filepath + ".csv" );
```

### Combined solutions

```
// user input
filepath = "C:/Users/teach/Desktop/imagej-courses-master/data/mitocheck-movie/EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif";
threshold = 18;

// close all images
run("Close All"); 

// open file
open( filepath );

// threshold
setThreshold( threshold, 255 );
run( "Convert to Mask" );

// configure measurements
run("Set Measurements...", "area display redirect=None decimal=3");

// perform particle analysis
run("Analyze Particles...", "  show=Nothing summarize");

// save results table
table_filepath = filepath + ".csv";
saveAs( "Results", table_filepath );
```

##### Note

It is not ideal to have the results in the same folder as the image. 
To avoid this we would have to split the filepath into a directory and filename...

## Making it really nice, with graphical user interface

It is nice, not to have to type into the macro, but enter the variables with a GUI.
In fact, it is not only nice, but also safer, because it prevents us from breaking the code by typing something wrong there. 

Typically, I use google to find out how to do something related to programming:

Google: imagej macro get variable from user
- http://imagej.1557.x6.nabble.com/Having-a-macro-prompt-for-variable-input-td3694090.html
- getNumber("prompt", defaultValue); 

**=> interactive practical, getting a number via the GUI and printing it**

### Activity: adding another GUI element

In below code the threshold variable already has its GUI.
Try to also **obtain the filepath from the GUI**.

Hint: 
- https://imagej.nih.gov/ij/macros/OpenDialogDemo.txt

```
// User input
filepath = "H:\\imagej-courses-master\\data\\mitocheck-movie\\EMBO_2012_Group3--empty--empty--W0002--P001--T00000--Z000--C.tif";
threshold = getNumber("Enter threshold", 29);

// General code
run("Image Sequence...", "open=["+directory+"] sort");
run("Set Measurements...", "area display redirect=None decimal=4");
setAutoThreshold("Default dark");
setThreshold(threshold, 255);
setOption("BlackBackground", false);
run("Convert to Mask", "method=Default background=Dark");
run("Analyze Particles...", "  show=Nothing summarize stack");
```

&nbsp;

&nbsp;

#### Solution

```
filepath = File.openDialog( "Select a File" );
```
 
### Activity: Saving the results table with at a good place and with a good name

Now that we have the input file as a variable, we can automatically save the results table with a name related to this file.
Try to do this on your own.

Hints:
- Google: ImageJ Macro save results table
- http://imagej.1557.x6.nabble.com/save-results-table-as-csv-with-custom-name-td5003427.html
- Google: ImageJ Macro create new folder
- https://stackoverflow.com/questions/36144914/imagej-macro-make-new-folder-and-save-output-in-new-folder 

#### Solution

...

## The final touch: functions

It is very good for readability and for reusing parts of our code to pack it into small parts that belong together, so-called "functions".

### Activity

Copy below code into Fiji. Note that everything related to measure the cells was wrapped into a function.
Try to also **make a function for the thresholding**.

```
// User input
//
filepath = File.openDialog("Select a File");
threshold = getNumber("Enter threshold", 29);

// Main
//

open( filepath );

// put into a function...
setAutoThreshold("Default dark");
setThreshold(threshold, 255);
setOption("BlackBackground", false);
run("Convert to Mask", "method=Default background=Dark"); 

measureCells();

// Functions
// 

function measureCells() {
	run("Set Measurements...", "area display redirect=None decimal=4");
	run("Analyze Particles...", "  show=Nothing summarize stack");
}  
```

Solution:
- ../macros/CountCells-Functions.ijm

## Analyzing multiple images in one folder

```
// This macro batch processes all the files in one folder ending with ".tif". 

dir = getDirectory("Choose a Directory ");
setBatchMode(true); // will run faster because it will not show all the windows

processFiles( dir );

function processFiles( dir ) {
   list = getFileList( dir );
   
   for (i = 0; i < list.length; i++) 
   {
   	path = dir + list[i];
        processFile( path );
   }
 
}

function processFile(path) {
     if (endsWith(path, ".tif")) {
         
         print( "Processing file: " + path );
         
         open( path );
         
         //
         // ADD OWN CODE HERE
         //
        
    }
}
```

### Activity

Add your own cell counting code!

## Analyzing multiple images in one folder, including sub-folders

Sometimes you have your image files distributed in sub-folders. Below code deals with this.

```
// "BatchProcessFolders"
//
// This macro batch processes all the files in a folder and any
// subfolders in that folder. In this example, it runs the Subtract 
// Background command of TIFF files. For other kinds of processing,
// edit the processFile() function at the end of this macro.

 dir = getDirectory("Choose a Directory ");
 setBatchMode(true); // will run faster because it will not show the windows
 processFiles(dir);

 function processFiles(dir) {
    list = getFileList(dir);
    for ( i = 0; i < list.length; i++) 
    {
        if ( endsWith(list[i], "/") ) {
            // recursively go into sub-folders
            processFiles( "" + dir + list[i] );
        }
        else 
        {
           path = dir + list[i];
           processFile( path );
        }
    }
}

function processFile(path) 
{     
     if (endsWith(path, ".tif")) 
     {
         
         print( "Processing file: " + path );
         
         open( path );
         
         //
         // ADD OWN CODE HERE
         //   
    }
}
```
