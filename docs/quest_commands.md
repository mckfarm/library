---
layout: default
title: Quest commands
---


**Basic Quest commands I use frequently. More details can be found on the [Quest knowledgebase page](https://kb.northwestern.edu/page.php?id=72406).**

## Logging in to Quest:
```
ssh -X NETID@quest.it.northwestern.edu
```

## Submitting an interactive session:
Interactive session - time in hours:min:sec
```
srun --account=b1042 --time=01:00:00 --partition=genomicsguest --mem=1G --pty bash -l
srun --account=p31629 --time=01:00:00 --partition=short --mem=2G --pty bash -l
```

## Basic navigation and job submissions
```
sbatch --test-only <job_submission_script>
cp <source> <destination>
mv <source> <destination>
scancel -u <NETID>
```


## Conda environments
[Conda environment management](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
```
module load python/anaconda3.6
conda create --prefix /path/to/folder -c -y
conda activate /location/of/environment
```

---

## Transferring to/from Wells NAS server
must be in log in node
```
lftp -p 22 -u mmf8608 sftp://129.105.86.3:5000/
```

---

## Dealing with tar files
```
tar -xvzf name.tar # x extract v verbose z gzip f filename
tar -tvf name.tar # t show file names
tar -xvf name.tar filename.txt # specify file names in filename.txt to extract from tar
tar -czvf name.tar # c create # z gzip # v verbose f filename
```

## Data management
How much data is in a directory (can take a long time)
```
du -sh /full/path/to/directory
```

Sort by size ====
```
du -d 1 /path/to/directory | sort -n
```

Is there room in your allocation?
```
checkproject <allocation>
```
