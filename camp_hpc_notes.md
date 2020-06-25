---


---

<h1 id="camp-cluster">CAMP Cluster</h1>
<p><a href="https://wiki.thecrick.org/display/HPC/Analysis+%7C+Simulation+%7C+Processing">https://wiki.thecrick.org/display/HPC/Analysis+%7C+Simulation+%7C+Processing</a></p>
<h1 id="useful-frequent-commands">Useful, frequent commands</h1>
<p>Delete bugged jobs yet to be processed</p>
<pre class=" language-bash"><code class="prism  language-bash">myq <span class="token operator">|</span> <span class="token function">sed</span> <span class="token string">'s/\|/ /'</span><span class="token operator">|</span><span class="token function">awk</span> <span class="token string">'{print <span class="token variable">$1</span>}'</span> <span class="token operator">|</span> <span class="token function">sed</span> <span class="token string">':a;N;<span class="token variable">$!ba</span>;s/\n/ /g'</span> <span class="token operator">&gt;</span> jobid.txt
<span class="token function">cat</span> jobid.txt
scancel PASTE excluding the int JOBID
</code></pre>
<p><a href="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/HPCandHTCusingLegion/1_intro_to_hpc.html">http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/HPCandHTCusingLegion/1_intro_to_hpc.html</a></p>
<p><img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/thecomputer_v2.png" alt="enter image description here"></p>
<p>Overview of Cluster design:</p>
<p>Internet &gt; login node (public) &gt; compute node (private)<br>
<img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/cluster-topology.png" alt=""></p>
<p>Symmetric Multi Processing (SMP) is used to paraellise computing jobs within a computer.<br>
<img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/smp2.png" alt=""></p>
<p>Compute cluster design:<br>
<img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/cluster.png" alt=""></p>
<p>The Interconnect determines the speed and bandwidth (MB/s)<br>
<img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/numa.png" alt=""><br>
10 Bbs ethernet = 15 microseconds MPI latency &gt; 800 MB/s bandwidth</p>
<p>GPU accelerator:<br>
<img src="http://github-pages.ucl.ac.uk/RCPSTrainingMaterials/assets/gpu.png" alt=""></p>
<h2 id="login">Login</h2>
<ul>
<li>Set up default on iTerm2 to open CAMP</li>
<li>To open non cluster window, right click on ITerm2 and open New Window —&gt; Olivers Mac</li>
<li>Once open, you will be on the Head Node. YOU MUST REQUEST AN INTERACTIVE SESSION TO GET ALLOCATED TO A COMPUTER (and come off the head node).</li>
</ul>
<h2 id="srun">srun</h2>
<ul>
<li>int (alias set up in home directory ~ &gt; nano .bash_profile —&gt; code to request interactive session srun: <code>alias int2='srun -N 1 -c 1 --mem=8G --pty --partition=int -t 12:00:00 /bin/bash'</code>
<ul>
<li>set partition as int. See all partitions with <code>sinfo</code></li>
</ul>
</li>
</ul>
<p>For more nodes &amp; memory:<br>
<code>alias int2='srun -N 1 -c 4 --mem=16G --pty --partition=int -t 12:00:00 /bin/bash'</code></p>
<p>To run two tasks within 1 interactive node specify:<br>
<code>srun -n 2 -N 1 -c 1 --mem=8G --pty --partition=int -t 12:00:00 /bin/bash</code>. When inside the job specify: <code>srun -n 1</code> for first task &amp; <code>srun -n1</code> for second task.</p>
<p>-mem limit is <strong>~120G</strong> on int partition.</p>
<ul>
<li>
<p>Alternative is to submit a job to the cluster (use sbatch command)</p>
</li>
<li>
<p>To check your status on the cluster type myq (alias for squeue -u ziffo —&gt; set up in <strong>.bashrc</strong> as .bash_profile doesn’t allow the command to work)</p>
</li>
<li>
<p><code>squeue</code> shows all jobs submitted to cluster</p>
</li>
</ul>
<h2 id="sbatch">sbatch</h2>
<p><a href="https://slurm.schedmd.com/pdfs/summary.pdf">https://slurm.schedmd.com/pdfs/summary.pdf</a><br>
<code>sbatch --help</code> in command line<br>
<a href="https://slurm.schedmd.com/overview.html">https://slurm.schedmd.com/overview.html</a><br>
<a href="https://www.youtube.com/watch?v=NH_Fb7X6Db0&amp;feature=relmfu">https://www.youtube.com/watch?v=NH_Fb7X6Db0&amp;feature=relmfu</a><br>
<a href="https://wiki.thecrick.org/display/HPC/Analysis+%7C+Simulation+%7C+Processing#Analysis%7CSimulation%7CProcessing-batch">https://wiki.thecrick.org/display/HPC/Analysis+%7C+Simulation+%7C+Processing#Analysis|Simulation|Processing-batch</a></p>
<p><code>sbatch --cpus-per-task=8 --mem 0 -N 1 --partition=cpu --time=12:00:00 --wrap="Rscript ~/home/projects/vcp_fractionation/expression/deseq2/DGE_plotsRmd.R"</code></p>
<p><code>N</code> is the number of nodes (usually leave at 1)<br>
<code>cpus-per-task</code> is the number of cores (i.e. threads - so you could change the STAR command to <code>runThreadN 8</code> with this example)<br>
Can cancel jobs sent to the cluster with <code>scancel</code> then job id (identified from <code>squeue -u ziffo</code>)<br>
Can remove the <code>slurm</code> files after download (that is just the log): <code>rm slurm-*</code></p>
<h3 id="memory">Memory</h3>
<p><code>--mem</code> is the amount of memory you want. Limit is 250G for cpu partition node &amp; 120G on int partition.<br>
<code>--mem=0</code> allocates the whole node so use this when you have a large job and not clear what limit to set. Then after job run <code>sacct</code> to check how much memory was actually used: shown under MaxRSS. Use this to correct for further jobs of same script.</p>
<pre><code>sacct -j &lt;jobid&gt; --format="CPUTime,MaxRSS"
sacct -j &lt;jobid&gt; -l # shows long list of all variables of the job. 
</code></pre>
<p>MaxRSS units are in Bytes with K.  1GB = 1,000,000,K</p>
<p>e.g. 240423400K = 240.4GB ~ rough limit on CPU node. In this case specify <code>hmem</code> instead e.g.:<br>
<code>sbatch --cpus-per-task=10 --mem=1500G -N 1 --partition=hmem -t 48:00:00 --wrap=</code></p>
<p>Also set ulimit to unlimited:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">ulimit</span> -s unlimited <span class="token comment"># to avoid C stack error</span>
<span class="token function">ulimit</span> -s <span class="token comment"># check that ulimit is set to unlimited</span>
</code></pre>
<p>394,734,200K = 394.7GB</p>
<p>1,221,176K<br>
762,923,372K</p>
<p>R needs to create a copy of everything that’s in memory for each individual process (it doesn’t utilize shared memory).<br>
Try:</p>
<ol>
<li><strong>Using fewer cores</strong>  - If you were trying to use 4 cores, it’s possible you have enough RAM to support 3 parallel threads, but not 4.  <code>registerDoMC(2)</code>, for example, will set the number of parallel threads to 2 (if you are using the  <code>doMC</code>  parallel backend).</li>
<li><strong>Using less memory</strong>  - without seeing the rest of your code, it’s hard to suggest ways to accomplish this. One thing that might help is figuring out which R objects are taking up all the memory (<a href="https://stackoverflow.com/questions/1395270/determining-memory-usage-of-objects">Determining memory usage of objects?</a>) and then removing any objects from memory that you don’t need (<code>rm(my_big_object)</code>)</li>
<li><strong>Adding more RAM</strong>  - if all else fails, throw hardware at it so you have more capacity.</li>
<li><strong>Sticking to single threading</strong>  - multithreaded processing in R is a tradeoff of CPU and memory. It sounds like in this case you may not have enough memory to support the CPU power you have, so the best course of action might be to just stick to a single core.</li>
</ol>
<h3 id="partitions">Partitions</h3>
<ul>
<li><code>batch</code> - batch processing using CPU-only compute nodes</li>
<li><code>int</code> - interactive use via command line/X-windows using CPU-only compute nodes</li>
<li><code>vis</code> - interactive use via command line/X-windows GPU-accelerated nodes for visualisation</li>
<li><code>gpu</code> - batch processing using GPU-accelerated nodes. for gpu you’d have to add --gres=gpu:&lt;1-4&gt; or try with only --exclusive</li>
<li><code>hmem</code> - batch processing using large RAM machines</li>
<li><code>sb</code>  - batch processing using GPU-accelerated nodes designed for Structural Biology applications</li>
</ul>
<h3 id="dependenies-hold-jobs">Dependenies hold jobs</h3>
<p><a href="https://hpc.nih.gov/docs/job_dependencies.html">https://hpc.nih.gov/docs/job_dependencies.html</a></p>
<p>Job dependencies are used to defer the start of a job until the specified dependencies have been satisfied. They are specified with the  <code>--dependency</code>  option to  <code>sbatch</code>  or  <code>swarm</code>  in the format</p>
<p>sbatch --dependency=<a>type:job_id[:job_id][,type:job_id[:job_id]]</a> …</p>
<p>e.g delay job until another is complete:<br>
<code>sbatch --dependency=afterany:6854906 -N 1 -c 8 --mem 200G -t 12:00:00 --wrap="Rscript ~/home/projects/vcp_fractionation/expression/deseq2/GO_plotsRmd.R"</code></p>
<p>Dependency types:</p>
<p><strong>after</strong>:jobid[:jobid…]<br>
job can begin after the specified jobs have started</p>
<p><strong>afterany</strong>:jobid[:jobid…]<br>
job can begin after the specified jobs have terminated</p>
<p><strong>afternotok</strong>:jobid[:jobid…]<br>
job can begin after the specified jobs have failed</p>
<p><strong>afterok</strong>:jobid[:jobid…]<br>
job can begin after the specified jobs have run to completion with an exit code of zero (see the  <a href="https://hpc.nih.gov/docs/userguide.html#exitcodes">user guide</a>  for caveats).</p>
<p><strong>singleton</strong><br>
jobs can begin execution after all previously launched jobs  <em>with the same name and user</em>  have ended. This is useful to collate results of a swarm or to send a notification</p>
<h2 id="set-up-alias">Set up Alias</h2>
<p>type <code>alias</code> into terminal to see already set alias’s:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">alias</span> egrep<span class="token operator">=</span><span class="token string">'egrep --color=auto'</span>
<span class="token function">alias</span> fgrep<span class="token operator">=</span><span class="token string">'fgrep --color=auto'</span>
<span class="token function">alias</span> grep<span class="token operator">=</span><span class="token string">'grep --color=auto'</span>
<span class="token function">alias</span> int<span class="token operator">=</span><span class="token string">'srun -N 1 -c 1 --mem=8G --pty --partition=int -t 12:00:00 /bin/bash'</span>
<span class="token function">alias</span> int2<span class="token operator">=</span><span class="token string">'srun -N 1 -c 4 --mem=16G --pty --partition=int -t 12:00:00 /bin/bash'</span>
<span class="token function">alias</span> l.<span class="token operator">=</span><span class="token string">'ls -d .* --color=auto'</span>
<span class="token function">alias</span> ll<span class="token operator">=</span><span class="token string">'ls -lh'</span>
<span class="token function">alias</span> ls<span class="token operator">=</span><span class="token string">'ls --color=auto'</span>
<span class="token function">alias</span> mc<span class="token operator">=</span><span class="token string">'. /usr/libexec/mc/mc-wrapper.sh'</span>
<span class="token function">alias</span> myq<span class="token operator">=</span><span class="token string">'squeue -u ziffo'</span>
<span class="token function">alias</span> oist<span class="token operator">=</span><span class="token string">'ssh -X oliver-ziff@login.oist.jp'</span>
<span class="token function">alias</span> sango<span class="token operator">=</span><span class="token string">'ssh -X oliver-ziff@sango.oist.jp'</span>
<span class="token function">alias</span> vi<span class="token operator">=</span><span class="token string">'vim'</span>
<span class="token function">alias</span> which<span class="token operator">=</span><span class="token string">'alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'</span>
</code></pre>
<h2 id="remove-passphrase-from-login">Remove passphrase from login</h2>
<p>open local mac terminal:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">nano</span> ~/.ssh/config
<span class="token comment">#add to start:</span>
HOST *
   UseKeychain <span class="token function">yes</span>
</code></pre>
<h2 id="run-programmes">Run Programmes</h2>
<p>On the cluster the is hundreds of different software already pre installed. To see them type <code>ml av</code> (modules available)</p>
<p>To run a specific software use module load command: eg <code>ml STAR</code></p>
<p>Remove a module that is already loaded:<br>
ml -module_name</p>
<h2 id="connect-to-sever">Connect to Sever</h2>
<p><a href="https://groups.oist.jp/it/accessing-file-servers-mac">https://groups.oist.jp/it/accessing-file-servers-mac</a><br>
On local Mac go to:<br>
Finder &gt; Click Go  &gt; Connect to Server &gt; Type server address <code>cifs://data.thecrick.org/lab-luscomben/</code> &gt; Connect</p>
<p>Or via Terminal ssh</p>
<h1 id="working-directory-and-symlinks---soft-links">Working directory and Symlinks - Soft-links</h1>
<p>The ~/ directory only has 5GB of space and running software out of this will rapidly take this up.<br>
To get around this need to create a soft-link from working/ to ~/ with:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">mv</span> ~/.nextflow/ /camp/lab/luscomben/home/
<span class="token function">ln</span> -s /camp/lab/luscomben/home/.nextflow ~/.nextflow

<span class="token function">mv</span> ~/.singularity/ /camp/lab/luscomben/home/
<span class="token function">ln</span> -s /camp/lab/luscomben/home/.singularity ~/.singularity

<span class="token function">mv</span> ~/.conda/ /camp/lab/luscomben/home/
<span class="token function">ln</span> -s /camp/lab/luscomben/home/.conda ~/.conda
</code></pre>
<p>Check the symlink <code>ls -lart</code></p>
<h1 id="permissions">Permissions</h1>
<p>When you make a new folder or file you need to also ensure you have permissions. Sometimes this will happen automatically.</p>
<p>Command <code>chmod -R 744 [file name]</code> will ensure that you have full access (ie execute, read, write); others in group &amp; public can read.</p>
<p><code>chmod +rwx [filename]</code></p>
<h2 id="change-permission-for-directory--all-subdirectories">Change permission for directory &amp; all subdirectories</h2>
<p><code>chmod -R 755 [directory name]</code></p>
<p>e.g. <code>chmod -R 755 ACD_CB1D_counts/</code></p>
<p>Alias <code>ll</code> will list all your active sessions in the cluster and show permissions (rwxr—r—r—)</p>
<h2 id="folders">Folders</h2>
<p>Bin<br>
Genomes &gt; Annotation; Sequences; Index<br>
Projects<br>
Scripts</p>
<h1 id="transfer-files-from-cluster-to-local-mac">Transfer files from Cluster to Local Mac</h1>
<p>Can use rsync after setting up in ~/.ssh/config:</p>
<pre><code>host camp-ext
    User ziffo
    hostname login.camp.thecrick.org
</code></pre>
<p>Go to target folder:<br>
<code>cd /Users/ziffo/'Dropbox (The Francis Crick)'/Medical\ Files/Research/PhD/single_cell_astrocytes/scRNAseq_astrocytes_motorneurons_plots</code></p>
<p>Then run:</p>
<p><code>rsync -aP camp-ext:/camp/home/ziffo/home/projects/vcp_fractionation/expression/deseq2/DGE_plots.html .</code></p>
<p><code>rsync -aP camp-ext:/camp/home/ziffo/home/projects/vcp_fractionation/expression/deseq2/GO_plots.html .</code></p>
<p><code>rsync -aP camp-ext:/camp/home/ziffo/home/projects/single_cell_astrocytes/expression/seurat/figures/ .</code></p>
<p><code>rsync -aP camp-ext:/camp/home/ziffo/home/projects/single_cell_astrocytes/expression/seurat/scripts/scRNAseq_seurat_separate.html .</code></p>
<h1 id="transfer-files-from-local-mac-to-cluster">Transfer files from Local Mac to Cluster</h1>
<p>On local mac terminal<br>
Use <code>scp</code><br>
specify path to file to transfer<br>
specify CAMP destination directory as <code>camp-ext:</code></p>
<p><code>scp rename.sh camp-ext:/camp/home/ziffo/home/projects/vcp_fractionation/reads</code></p>
<h1 id="restore-from-camp-backup">Restore from CAMP Backup</h1>
<p>we can access the daily snapshots over the last week ourselves via <code>/camp/lab/luscomben/.snapshots/</code></p>
<p>Go to the working target folder within camp then run rsync e.g.:</p>
<p><code>rsync -aP /camp/lab/luscomben/.snapshots/crick.camp.lab.luscomben.19-12-19/home/projects/vcp_fractionation/reads .</code></p>
<p>Can also do via Finder &amp; enabling ‘show hidden files’ on your device.<br>
Ensure you have saved all your work on your laptop before carrying out the following:</p>
<ol>
<li>Open terminal</li>
<li>Enter the following: defaults write com.apple.Finder AppleShowAllFiles true [Press return]</li>
<li>Enter the following in terminal: killall Finder [Press return]</li>
</ol>
<p>Once you’ve done this, if you launch CAMP, you’ll see a folder called ‘.snapshots’ - this will have snapshots of CAMP for the past week, where you can restore your directory from.</p>
<p><code>/camp/lab/luscomben/.snapshots/crick.camp.lab.luscomben.19-12-19/home/projects/vcp_fractionation/reads</code></p>
<h1 id="tmux">tmux</h1>
<p><a href="https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/">https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/</a></p>
<p>Terminal multiplexer.<br>
2 features:</p>
<ol>
<li>Terminal window management</li>
<li>Terminal session management</li>
</ol>
<p>Open multiple windows of terminal without the need to open multiple terminal emulator windows.<br>
decatching: exit a session but keep it active until you kill the tmux server<br>
attaching: pick up the detached session at anytime.</p>
<h2 id="sessions">Sessions</h2>
<p>start session<br>
<code>tmux</code></p>
<p>list sessions<br>
<code>tmux ls</code></p>
<p>create a new session<br>
<code>tmux new-window (prefix + c)</code></p>
<p>creates a new tmux session named session_name<br>
<code>tmux new -s session_name</code></p>
<p>rename the current window<br>
<code>tmux rename-window (prefix + ,)</code></p>
<h2 id="windows">Windows</h2>
<p>closing window<br>
<code>exit</code></p>
<p>attaches to an existing tmux session named session_name<br>
<code>tmux attach -t session_name</code></p>
<p>switch to an existing session named session_name<br>
<code>tmux switch -t session_name</code></p>
<p>lists existing tmux sessions<br>
<code>tmux list-sessions</code></p>
<p>detach the currently attached session<br>
<code>tmux detach (prefix + d)</code></p>
<h2 id="navigating-panes">Navigating Panes</h2>
<p><code>tmux split-window (prefix + ")</code><br>
splits the window into two vertical panes</p>
<p><code>tmux split-window -h (prefix + %)</code><br>
splits the window into two horizontal panes</p>
<p><code>tmux swap-pane -[UDLR] (prefix + { or })</code><br>
swaps pane with another in the specified direction</p>
<p><code>tmux select-pane -[UDLR]</code><br>
selects the next pane in the specified direction</p>
<p><code>tmux select-pane -t :.+</code><br>
selects the next pane in numerical order</p>
<h2 id="other-useful-tmux-commands">Other useful tmux commands</h2>
<p>lists out every bound key and the tmux command it runs<br>
<code>tmux list-keys</code></p>
<p>lists out every tmux command and its arguments<br>
<code>tmux list-commands</code></p>
<p>lists out every session, window, pane, its pid, etc.<br>
<code>tmux info</code></p>
<p>reloads the current tmux configuration (based on a default tmux config)<br>
<code>tmux source-file ~/.tmux.conf</code></p>
<p><code>tmux select-window -t :0-9 (prefix + 0-9)</code><br>
move to the window based on index</p>
<p>To connect to that session you start tmux again but this time tell it which session to attach to:<br>
tmux attach -t 0<br>
Note that the <code>-t 0</code> is the parameter that tells tmux which session to attach to. “0” is the first part of your <code>tmux ls</code> output.</p>
<p>rename session<br>
tmux rename-session -t 0 database<br>
The next time you attach to that session you simply use <code>tmux attach -t database</code>. If you’re using multiple sessions at once this can become an essential feature.</p>
<h2 id="tmux-status-bar">tmux status bar</h2>
<p>Left: currently opened window<br>
Right: time &amp; date</p>
<h1 id="jupyter-notebooks">Jupyter notebooks</h1>
<p>Begin an interactive job using <code>srun --ntasks=1 --cpus-per-task=1 --partition=int --time=8:00:0 --mem=8G --pty /bin/bash</code> .<br>
Activate conda environment <code>source activate rtest</code><br>
run command for <code>jupyter-lab</code>:</p>
<pre class=" language-bash"><code class="prism  language-bash">unset XDG_RUNTIME_DIR<span class="token punctuation">;</span>  
jupyter-lab --ip<span class="token operator">=</span><span class="token punctuation">$(</span>hostname -I <span class="token operator">|</span> <span class="token function">awk</span> <span class="token string">'{print <span class="token variable">$1</span>}'</span><span class="token punctuation">)</span> --port<span class="token operator">=</span>8080
</code></pre>
<p>Copy &amp; paste the URL into web brower eg</p>
<pre><code>http://10.28.2.10:8081/?token=9767b1d01a2f2de3ca22193b51ba43704d05595863745677
</code></pre>

