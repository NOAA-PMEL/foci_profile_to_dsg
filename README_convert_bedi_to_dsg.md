#foci_profile_to_dsg

###Ferret scripts to write FOCI profile data DSG files

README for converting BEDI Epic-netCDF files to dsg files for the collections in ERDDAP, arcticRescueData, chukchi, and Shelikof_line8.  All the scripts and related files are in SVN under projects/bedi/scripts

To convert a single file, we need the scripts write_dsg_bedi.jnl, and rename_epic.jnl (called by write_dsg_bedi).  The files read a dataset names_units_titles.nc which contains a list of variable names, titles, and units, based on the spreadsheet developed with Peggy Sullivan. To convert a file, give the source directory, the destination directory, and the 
filename, without ext ension.  So, to convert this file:

/home/users/ansley/projects/bedi/Shelikof_line8/2012/dy1204c002.cdf

run ferret, and execute the script:

  > ferret

  yes? go write_dsg_bedi /home/users/ansley/projects/bedi/Shelikof_line8/2012 \
  /home/data/bedi/Shelikof_line8/2012 dy1204c002

This writes /home/data/bedi/Shelikof_line8/2012/dy1204c002_dsg.nc

That's all that is needed for adding further datasets to any of our existing data.

------
Following are notes about setting up the datasets for ERDDAP configuration, and about creating the file with names,units,etc from the naming spreadsheet.

# The file names_units_titles.nc contains epic code, variable names, varible titles, variable units and the additional notes field. To generate this:
  Export the spreadsheet from https://docs.google.com/spreadsheets/d/1ijLKbgrsJFOZVOIEG7jJg-kxz8e2Fni8QskqIphLF4w
  as a csv file.  Edit this taking off the start and end rows, leaving just a single header line and the columns.  Make sure there aren't any commas within cells, e.g. OXYGEN, %SAT. 
  
  Remove any quotes. Also remove ** and *** that are pointing to notes. Also check for quotes around the units that are part of some long-names. Call this names_spreadsheet.csv. Convert to a netcdf file names_units_titles.nc with csv_to_nc.jnl.

# The script write_dsg_bedi.jnl uses this to transform the epic .nc files to dsg files. It calls rename_epic_bedi.jnl which reads the info from file names_units_titles.nc created in the step above and the translation for each Epic code to new variable names with units.  

# The scripts dsg_arcticRescue.jnl  dsg_chukchi.jnl  dsg_Shelikof_line8.jnl will run the conversion script on the whole list of files under those directories. To do just one file, the Ferret command runs the script write_dsg_bedi.jnl, naming the directory where the epic netCDF file is, and the file name, without extension.  So run this:

     > ferret
     yes? go write_dsg_bedi /home/users/ansley/projects/bedi/chukchi/ae1001_2010 \
     /home/users/ansley/projects/bedi/chukchi/ae1001_2010 ae1001c019
  
  which will take the epic file

    /home/users/ansley/projects/bedi/chukchi/ae1001_2010/ae1001c019.nc

  and convert it to 

    /home/users/ansley/projects/bedi/chukchi/ae1001_2010/ae1001c019_dsg.nc


-- details about configuration of the dataset:

# We need the whole range of depths and a list of all variable names in each overall set, to create
  a file with all variables at all depths. 

  o From each main data directory, chukchi, arcticRescue, and Shelikof_line8, the shell script
    list_all_vars.sc makes a file vars.sort with the variable names contained in the files. 

  o From each main data directory, > source ../scripts/list_all_depths.sc
    which makes a file depths.list with the size of the depth dimension in each file. 

# Find the file having the most depths in depths.list, and use the list of variable 
  names, to update the scripts scripts/dummy_file_Shelikof_line8.jnl etc. This also 
  uses the rename_epic_bedi.jnl file to get the new variable names, units, etc. For any 
  variables that are not in this deepest depths file and writes missing-data with that 
  variable. For configuring thisfile contains all variables and depths. Run the 
  dummy_file*.jnl scripts to write allvars_Shelikof_line8.nc, etc. These are also run 
  from each data directory by the script convert_epic_dsg.sc

# Make data directories under /home/data/bedi/ with the same structure as under these local
  data directories.  Copy all the dsg.nc files to the subdirectories. Each directory has a file
  copy_files.sc to do this. The allvars_*.nc from the scripts/directory is copied to each upper 
  level /home/data/bedi/ directory. Then ERDDAP is configured from the allvars files.


