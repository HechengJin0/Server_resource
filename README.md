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
in crc: `mkdir my_folder`
in storinator:
`rsync -r my_folder crc:/bgfs/tibrahim/hej24/my_folder`

crc-> storinator
in storinator
`rsync -avh crc:/bgfs/tibrahim/.... /home/... --remove-source-files`

## run singulairty in crc
`module load singularity`     
`singularity pull fmriprep_23.0.0.sif docker://nipreps/fmriprep:23.0.0`

## mount cluster:    
`sshfs -o kill_on_unmount,reconnect,allow_other hej24@cluster:/mnt/storinator cluster_mount`   
`sshfs -o kill_on_unmount,reconnect,allow_other hej24@crc:/bgfs/tibrahim /Users/jin/crc_mount`   
`diskutil umount force /Users/jin/crc_mount`   

## GUI interface
Web Portal Method:  
https://crc.pitt.edu/viz

## matlab interface
`module load matlab`  
`matlab`

## mricron interface
`module load mricron`  
`matlab`

## Storage
personal 75GB: `/ihome/tibrahim/hej24`  
group 5TB: `/bgfs/tibrahim/`

## allocate multi core in crc
`crc-interactive.py --smp --time=12 --num-cores=8 --mem=10`    
potential error message: 
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
