# CRC
## Cluster Access 
`ssh hej24@htc.crc.pitt.edu`  
or `ssh hej24@h2p.crc.pitt.edu`

## Check space
`crc-quota.py`  
`crc-usage.pl`
`du -sh .`

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
group 5TB: `/bgfs/tibrahim/tad64/`



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
   HostName h2t.crc.pitt.edu
   User hej24
   IdentityFile %d/.ssh/hj_crc_key
```
