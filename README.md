# CRC
## Cluster Access 
`ssh hej24@htc.crc.pitt.edu`  
or `ssh hej24@h2p.crc.pitt.edu`

## Check space
`crc-usage.py` 
`crc-quota.py`  
`du -sh .`     
subfolder size: `du -sh *`    
count files: `ls -1 | wc -l`

## copy data
storinator -> crc
in storinator:
`rsync -r my_folder crc:/bgfs/tibrahim/hej24/`
remember: do not put / in `my_folder`, otherwises it transfers subfolders to /hej24/

crc-> storinator
in storinator
`rsync -avh crc:/bgfs/tibrahim/.... /home/... --remove-source-files`

## run singulairty in crc
`module load singularity`     
`singularity pull fmriprep_23.0.0.sif docker://nipreps/fmriprep:23.0.0`

## mount cluster:    
`sshfs -o kill_on_unmount,reconnect,allow_other hej24@cluster:/mnt/storinator cluster_mount`   
`sshfs -o kill_on_unmount,reconnect,allow_other hej24@crc:/ix1/tibrahim /Users/jin/crc_mount`   
`sshfs -o kill_on_unmount,reconnect,allow_other hej24@crc:/ix1/haizenstein/hej24 crc_gpn_mount`

`diskutil umount force /Users/jin/crc_mount`   

## GUI interface
Web Portal Method:  
https://crc.pitt.edu/viz

## matlab/mricron interface
`module load matlab`  
`module load mricron`  

## Storage
personal 75GB: `/ihome/tibrahim/hej24`  
group 5TB: `/bgfs/tibrahim/`

## allocate multi core in crc
`crc-interactive.py --smp --time=12 --num-cores=8 --mem=10`    

## potential error message run matlab parpool: 
```
>> parpool(8)
Starting parallel pool (parpool) using the 'local' profile ...
Error using parpool (line 113)
Parallel pool failed to start with the following error. For more detailed information, validate the profile 'local' in the Cluster
Profile Manager.

Caused by:
    Error using parallel.internal.pool.InteractiveClient>iThrowWithCause (line 668)
    Failed to locate and destroy old interactive jobs.
        Error using parallel.Cluster/findJob (line 74)
        Unknown type: concurrentconcurrent.
```
fixed by https://www.mathworks.com/matlabcentral/answers/92124-why-am-i-unable-to-use-parpool-with-the-local-scheduler-or-validate-my-local-configuration-of-parall `5) Clear the local scheduler data folder`
```
>> prefdir
ans =
    '/ihome/tibrahim/hej24/.matlab/R2019b'
>> exit
rm -r /ihome/tibrahim/hej24/.matlab/local_cluster_jobs
matlab
```

## freesurfer install
```
wget https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/7.4.1/freesurfer-linux-centos7_x86_64-7.4.1.tar.gz
tar -xzvf freesurfer-linux-centos8_x86_64-7.3.2.tar.gz
```
## BrainVein install
```
wget https://www.vtk.org/files/release/9.3/VTK-9.3.0.tar.gz
tar -xzvf VTK-9.3.0.tar.gz
[hej24@login1 VTK_build]$ pwd
/ihome/tibrahim/hej24/BrainVein/VTK_build
cmake -DCMAKE_BUILD_TYPE=Release -S ../VTK-9.3.0/ -B /ihome/tibrahim/hej24/BrainVein/VTK_build/
cmake --build /ihome/tibrahim/hej24/BrainVein/VTK_build/
```
VTK_build takes 1.5h in crc. 
[100%] Built target DomainsChemistryOpenGL2 

```
git clone https://github.com/InsightSoftwareConsortium/ITK
[hej24@login1 ITK_build]$ pwd
/ihome/tibrahim/hej24/BrainVein/ITK_build
```
error
```
[hej24@login1 ITK_build]$ cmake -DCMAKE_BUILD_TYPE=Release -S  ../ITK/ -B .
-- The CXX compiler identification is GNU 4.8.5
-- The C compiler identification is GNU 4.8.5
-- Detecting CXX compiler ABI info
CMake Error in /ihome/tibrahim/hej24/BrainVein/ITK_build/CMakeFiles/CMakeTmp/CMakeLists.txt:
  Target "cmTC_44193" requires the language dialect "CXX17" , but CMake does
  not know the compile flags to use to enable it.


CMake Error at /usr/local/share/cmake-3.20/Modules/CMakeDetermineCompilerABI.cmake:49 (try_compile):
  Failed to generate test project build system.
Call Stack (most recent call first):
  /usr/local/share/cmake-3.20/Modules/CMakeTestCXXCompiler.cmake:26 (CMAKE_DETERMINE_COMPILER_ABI)
  CMakeLists.txt:46 (project)

[hej24@login0 ITK_build]$ cmake -S ../ITK -B .
CMake Error at CMakeLists.txt:15 (cmake_minimum_required):
  CMake 3.16.3...3.19.7 or higher is required.  You are running version
  2.8.12.2
```

```
wget https://github.com/Kitware/CMake/releases/download/v3.28.3/cmake-3.28.3-linux-x86_64.tar.gz
tar -xzvf cmake-3.28.3-linux-x86_64.tar.gz
```

local: https://intranet.neuro.polymtl.ca/geek-tips/programming-languages/c%2B%2B/installation-of-vtk-and-itk-on-mac-os-x.html

1. Download VTK source
```
cd /Users/jin/BrainVein
wget https://www.vtk.org/files/release/9.3/VTK-9.3.0.tar.gz
tar -xzvf VTK-9.3.0.tar.gz
```
2. Create a new folder inside, and open cmake gui
```
jin@Jins-Mac-mini VTK-9.3.0 % pwd
/Users/jin/BrainVein/VTK-9.3.0
mkdir VTKBuild
cd VTKBuild
ccmake ..
```
3. change options
```
press 'c' #empty cache
CMAKE_INSTALL_PREFIX=/usr/local/vtk
press 'g'
```
4. configure and generate 
cmake ..  
make -j 4 #multi cores
```
[100%] Linking CXX shared library ../../lib/libvtkViewsInfovis-9.3.dylib
[100%] Built target ViewsInfovis
5.
```
sudo make install
```
1. Download ITK source
```
cd /Users/jin/BrainVein
git clone https://github.com/InsightSoftwareConsortium/ITK
```
2. Create a new folder inside, and open cmake gui
```
jin@Jins-Mac-mini ITKBuild % pwd
/Users/jin/BrainVein/ITK/ITKBuild
ccmake ..
```
3. change options
```
press 'c' #empty cache
CMAKE_INSTALL_PREFIX=/usr/local/itk
Module_ITKVtkGlue=ON
VTK_DIR=/usr/local/vtk/lib/cmake/vtk-6.0
press 'g'
```




## Useful link
https://burntyellow.github.io/#6 


# Create ssh config file

## In server (CRC): 
```
mkdir -p $HOME/.ssh 
chmod 0700 $HOME/.ssh
ssh-keygen
touch ~/.ssh/authorized_keys
cat ./hj_crc_key.pub >> authorized_keys 
```

`ssh-keygen` generates `hj_crc_key` and `hj_crc_key.pub`
Note: Enter file name as `hj_crc_key`


## In local: 
(If brand new)
```
mkdir -p $HOME/.ssh 
chmod 0700 $HOME/.ssh
touch ~/.ssh/config
```

(If not)
```
cd ~/.ssh
``` 

### Copy and rename key
```
scp hej24@htc.crc.pitt.edu:/ihome/tibrahim/hej24/.ssh/hj_crc_key . 
scp hej24@htc.crc.pitt.edu:/ihome/tibrahim/hej24/.ssh/hj_crc_key.pub . 
vim config 
```
### Edit `config`
```
Host crc
   HostName h2p.crc.pitt.edu
   User hej24
   IdentityFile %d/.ssh/hj_crc_key
```
