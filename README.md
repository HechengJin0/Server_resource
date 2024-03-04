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
# BrainVein install

install local: https://intranet.neuro.polymtl.ca/geek-tips/programming-languages/c%2B%2B/installation-of-vtk-and-itk-on-mac-os-x.html
#### VTK install
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
```
cmake ..  
make -j 4 #multi cores
```
[100%] Linking CXX shared library ../../lib/libvtkViewsInfovis-9.3.dylib
[100%] Built target ViewsInfovis
5.
```
sudo make install
```
#### ITK install
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
press 't' # advanced options
CMAKE_INSTALL_PREFIX=/usr/local/itk
Module_ITKVtkGlue=ON
VTK_DIR=/usr/local/vtk/lib/cmake/vtk-9.3
press 'g'
```
4. configure and generate 
```
cmake ..  
make -j 4 #multi cores
```
[100%] Built target ITKIONIFTI-all
5.
```
sudo make install
```
#### BrainVein install
1. Download BrainVein source
```
unzip src\ copy\ 34.zip
mv src\ copy\ 34 BrainVein_src
```
2. Create a new folder inside, and open cmake gui
```
cd BrainVein_src
mkdir BrainVeinBuild
cd BrainVeinBuild
ccmake ..
```
3. change options
```
press 'c' #empty cache
press 't' # advanced options
CMAKE_INSTALL_PREFIX=/usr/local/brainvein
ITK_DIR=/usr/local/itk/lib/cmake/ITK-5.4
press 'g'
```
4. configure and generate 
```
cmake ..  
make -j 4 #multi cores
```
warning during make: /Users/jin/BrainVein/BrainVein_src/bvImageArrayDilationShells.cpp:84:57: warning: implicit conversion from 'int' to 'unsigned short' changes value from 2000000000 to 37888 [-Wconstant-conversion]

binary saved in : /Users/jin/BrainVein/BrainVein_src/BrainVeinBuild/BrainVein.app/Contents/MacOS/BrainVein

5. Errors fixed
5.1 `make`
```
error:
   bvImageArrayDilationShells.cpp:625:33: error: use of undeclared identifier 'FALSE'
        bool branchPointFound = FALSE;
fix: change FALSE -> false, TRUE -> true
```
5.2 `BrainVein binary`
```
error: 42	Assertion failed: (x < m_imageDimensionX), function GetNumberOfNonZeroVoxelsWithIndexX, file bvImageArrayUShort3D.cpp, line 106.
fix: // assert(x < m_imageDimensionX);
re-do step2-4 (re-compile)

error: vtkRayCastImageDisplayH:9     WARN| Error: no override found for 'vtkRayCastImageDisplayHelper'. zsh: segmentation fault
fix: vtk_module_autoinit(TARGETS BrainVein MODULES ${VTK_LIBRARIES} )  # add find_package in CMakeLists.txt
```
5.3 warning `BrainVein binary`
```
warning: WARN| vtkOpenGLVolumeOpacityTable (0x600003516eb0): This OpenGL implementation does not support the required texture size of 65536, falling back to maximum allowed, 16384.This may cause an incorrect lookup table mapping.
fix: 
```

## Useful link
https://burntyellow.github.io/#6 

# wmh_seg setup
check https://github.com/jinghangli98/wmh_seg/blob/main/wmh.yml
```
module load gcc/8.2.0
module load python/anaconda3.9-2021.11

conda create --name wmhseg : environment location: /ihome/tibrahim/hej24/.conda/envs/wmhseg
source activate wmhseg
pip install nibabel
        - torch==1.11.0
        - torchvision==0.12.0
        - nibabel
        - einops
        - segmentation-models-pytorch==0.3.2
        - torchio

```

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
