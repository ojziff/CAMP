---


---

<blockquote>
<h1 id="nextflow">Nextflow</h1>
</blockquote>
<h1 id="concepts">Concepts</h1>
<p>Processes (steps) of a pipline are executed independently of each other and communicate via channels (asynchronous FIFO queues).<br>
Each process can have one or more channels as input and output.<br>
A single nextflow script contains each process with inputs &amp; outputs for each. The order the processes are written does not determine the order the processes are executed.<br>
Communication link between processes sets the order in which processes are exercuted.<br>
The configeration title defined the target execution platform (local computer, HPC or cloud).</p>
<h1 id="cluster">Cluster</h1>
<p>submit with screen or tmux:<br>
<code>screen -S nextflow -m bash -c 'sh run.sh; exec sh'</code></p>
<p><a href="https://github.com/chris-cheshire/linux-cheat-sheet">https://github.com/chris-cheshire/linux-cheat-sheet</a></p>
<p>submit nextflow pipelines as an sbatch:</p>
<pre class=" language-bash"><code class="prism  language-bash">mlnf <span class="token comment"># alias ml Nextflow/19.10.0  Singularity/2.6.0-foss-2016b Graphviz</span>
<span class="token function">cd</span> <span class="token variable">$baseDir</span>
sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --wrap<span class="token operator">=</span><span class="token string">"nextflow run main-test.nf -resume"</span> --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk
</code></pre>
<h1 id="nf-core">nf-core</h1>
<p><a href="https://nf-co.re/usage/configuration">https://nf-co.re/usage/configuration</a><br>
<a href="https://github.com/nf-core/configs/blob/master/docs/crick.md">https://github.com/nf-core/configs/blob/master/docs/crick.md</a><br>
<a href="https://www.dropbox.com/s/fv4uucjxm4ehckj/Phil%20Ewels%20-%20nf-core%20London%202020%20tips%20tricks.pdf?dl=0">https://www.dropbox.com/s/fv4uucjxm4ehckj/Phil%20Ewels%20-%20nf-core%20London%202020%20tips%20tricks.pdf?dl=0</a></p>
<h2 id="nf-corernaseq">nf-core/rnaseq</h2>
<pre class=" language-bash"><code class="prism  language-bash">mlnf
<span class="token function">cd</span> /camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/nf-core-rnaseq
sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk --wrap<span class="token operator">=</span>\
<span class="token string">"nextflow run nf-core/rnaseq \
	-profile crick \
	--reads '/camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/reads/*_R{1,2}.fastq.gz' \
	--fasta '/camp/home/ziffo/home/genomes/ensembl/Homo_sapiens.GRCh38.dna.primary_assembly.fa' \
	--transcript_fasta '/camp/home/ziffo/home/genomes/ensembl/Homo_sapiens.GRCh38.cdna.all.fa' \
	--gtf '/camp/home/ziffo/home/genomes/annotation/Homo_sapiens.GRCh38.99.gtf' \
	--star_index '/camp/home/ziffo/home/genomes/ensembl/GRCh38.99_ensembl_STAR_index' \
	--saveReference \
	--reverseStranded \
	--aligner star --saveAlignedIntermediates \
	--pseudoaligner salmon \
	--outdir '/camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/nextflow' \
	-with-singularity /camp/apps/misc/stp/babs/nf-core/singularity/rnaseq/1.4.2/nfcore-rnaseq-1.4.2.img \
	-resume"</span>
</code></pre>
<p>10292126</p>
<p>with additions: VASTools, kallisto</p>
<pre class=" language-bash"><code class="prism  language-bash">sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk --wrap<span class="token operator">=</span>\
<span class="token string">"nextflow run rnaseq-master/main.nf \
	-profile crick \
	--reads '/camp/home/ziffo/home/projects/nextflow-test/reads/*_R{1,2}.fastq.gz' \
	--fasta '/camp/home/ziffo/home/genomes/sequences/human/Homo_sapiens.GRCh38.dna.alt.fa' \
	--transcript_fasta '/camp/home/ziffo/home/genomes/index/kallisto/Homo_sapiens.GRCh38.cdna.all.fa' \
	--gtf '/camp/home/ziffo/home/genomes/annotation/Homo_sapiens.GRCh38.99.gtf' \
	--saveReference \
	--reverseStranded \
	--aligner star --saveAlignedIntermediates \
	--pseudoaligner salmon \
	--outdir '/camp/home/ziffo/home/nextflow/nf-core/rnaseq' \
	-with-singularity /camp/apps/misc/stp/babs/nf-core/singularity/rnaseq/1.4.2/nfcore-rnaseq-1.4.2.img \
	-resume"</span>
</code></pre>
<h2 id="edit-nf-core-pipeline">Edit nf-core pipeline</h2>
<ol>
<li>
<p>Fork &amp; Clone github page<br>
Clone it to the nf-core working directory on CAMP</p>
</li>
<li>
<p>Edit the Conda env by adding to the environment.yml file<br>
add kallisto &amp; vcftools bioconda packages &amp; specify versions:</p>
</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">  - kallisto<span class="token operator">=</span>0.46.2
  - vcftools<span class="token operator">=</span>0.1.16
</code></pre>
<ol start="3">
<li>Edit the Dockerfile<br>
add vast-tools with</li>
</ol>
<pre class=" language-docker"><code class="prism  language-docker"><span class="token keyword">RUN</span> git clone https<span class="token punctuation">:</span>//github.com/vastgroup/vast<span class="token punctuation">-</span>tools.git &amp;&amp; cd vast<span class="token punctuation">-</span>tools/ &amp;&amp;  echo <span class="token punctuation">-</span>ne <span class="token string">"y\Hsa"</span> <span class="token punctuation">|</span> ./install.R
<span class="token keyword">ENV</span> PATH ~/bin/vast<span class="token punctuation">-</span>tools<span class="token punctuation">:</span>$PATH
<span class="token keyword">RUN</span> echo <span class="token string">'export PATH=~/bin/vast-tools:$PATH'</span> <span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span> ~/.bashrc
</code></pre>
<p>These commands are from <a href="https://github.com/vastgroup/vast-tools">https://github.com/vastgroup/vast-tools</a></p>
<ol start="4">
<li>Build Docker Image:</li>
</ol>
<p>Make a login account on docker hub online <a href="https://hub.docker.com/?ref=login">https://hub.docker.com/?ref=login</a><br>
Ask Chris Cheshire to add you to luslab on docker<br>
Install Docker on mac <a href="https://hub.docker.com/?ref=login">https://hub.docker.com/?ref=login</a> NB big file 675MB!<br>
Log into your account on luslab on docker desktop<br>
Open Terminal on local Mac<br>
mkdir then cd bioinformatics/docker/rnaseq</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">rsync</span> -aP camp-ext:/camp/home/ziffo/home/nextflow/nf-core/rnaseq/rnaseq/environment.yml <span class="token keyword">.</span>
<span class="token function">rsync</span> -aP camp-ext:/camp/home/ziffo/home/nextflow/nf-core/rnaseq/rnaseq/Dockerfile <span class="token keyword">.</span>
</code></pre>
<p>Use Chris cheat sheet for commands on how to build the docker image: <a href="https://github.com/chris-cheshire/linux-cheat-sheet">https://github.com/chris-cheshire/linux-cheat-sheet</a></p>
<pre class=" language-docker"><code class="prism  language-docker">docker build <span class="token punctuation">-</span>t luslab/oli<span class="token punctuation">-</span>nfcore<span class="token punctuation">-</span>rnaseq<span class="token punctuation">-</span>dev<span class="token punctuation">:</span>1.0 /Users/ziffo/bioinformatics/docker/rnaseq <span class="token comment"># build new image from docker file</span>
docker images  <span class="token comment"># View local images</span>
docker run <span class="token punctuation">-</span>it luslab/oli<span class="token punctuation">-</span>nfcore<span class="token punctuation">-</span>rnaseq<span class="token punctuation">-</span>dev<span class="token punctuation">:</span>1.0 /bin/bash  <span class="token comment"># Run a container with an interactive shell</span>
docker push luslab/oli<span class="token punctuation">-</span>nfcore<span class="token punctuation">-</span>rnaseq<span class="token punctuation">-</span>dev<span class="token punctuation">:</span>1.0  <span class="token comment"># Pushes local image to luslab docker hub </span>
</code></pre>
<ol start="5">
<li>Edit<code>main.nf</code> on github repro luslab/oz-bulk-rnaseq</li>
</ol>
<p>Add processes at the end of script<br>
edit STAR memory<br>
edit vast-tools merge file &amp; point <a href="http://main.nf">main.nf</a> vast-tools process to this<br>
commit changes &amp; push to Github on Github desktop</p>
<ol start="6">
<li>Pull the docker .img (container) with singularity to the appropriate folder<br>
<a href="https://singularity.lbl.gov/docs-pull">https://singularity.lbl.gov/docs-pull</a></li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">mlnf
<span class="token function">cd</span> /camp/home/ziffo/home/nextflow/singularity
sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk --wrap<span class="token operator">=</span><span class="token string">"singularity pull  --name luslab-oli-nfcore-rnaseq-dev-1.0.img docker://luslab/oli-nfcore-rnaseq-dev:1.0"</span> 

sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk --wrap<span class="token operator">=</span><span class="token string">"docker pull luslab/oli-nfcore-rnaseq-dev:1.0"</span>
</code></pre>
<ol start="7">
<li>Run the Nextflow command as an sbatch:</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">mlnf
<span class="token function">cd</span> /camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/oli-nfcore-rnaseq-dev
nextflow pull ojziff/rnaseq <span class="token comment">## UPDATE PIPLINE  </span>

<span class="token comment">## RUN PIPELINE  </span>
sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk --wrap<span class="token operator">=</span>\
<span class="token string">"nextflow run ojziff/rnaseq \
	-with-singularity /camp/home/ziffo/home/nextflow/singularity/luslab-oli-nfcore-rnaseq-dev-1.0.img \
	-profile crick \
	--reads '/camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/reads/*_R{1,2}.fastq.gz' \
	--reverseStranded \
	--fasta '/camp/home/ziffo/home/genomes/sequences/human/Homo_sapiens.GRCh38.dna.alt.fa' \
	--transcript_fasta '/camp/home/ziffo/home/genomes/index/kallisto/Homo_sapiens.GRCh38.cdna.all.fa' \
	--gtf '/camp/home/ziffo/home/genomes/annotation/Homo_sapiens.GRCh38.99.gtf' \
	--bed12 '/camp/home/ziffo/home/genomes/annotation/Homo_sapiens.GRCh38.99.bed' \ 
	--saveReference \
	--aligner star --saveAlignedIntermediates \
	--pseudoaligner salmon \
	--outdir '/camp/home/ziffo/home/projects/astrocyte-fractionation-bulk-rnaseq/oli-nfcore-rnaseq-dev' \
    --email oliver.ziff@crick.ac.uk \  
	-resume"</span>
</code></pre>
<p>export NXF_WORK="/camp/home/ziffo/home/nextflow/projects/scrnaseq-repro"</p>
<h3 id="remove-containers--images-from-local-mac">Remove Containers &amp; Images from local Mac</h3>
<p>Containers &amp; images are huge so need to be pruned regularly.</p>
<pre class=" language-bash"><code class="prism  language-bash">docker container prune <span class="token comment"># removes all stopped containers</span>
docker image <span class="token function">rm</span> * <span class="token comment"># removes all images - replace * with image ID to remove individual IDs</span>
</code></pre>
<h2 id="nf-corescrnaseq">nf-core/scrnaseq</h2>
<p><a href="https://github.com/nf-core/scrnaseq/blob/master/main.nf">https://github.com/nf-core/scrnaseq/blob/master/main.nf</a></p>
<pre class=" language-bash"><code class="prism  language-bash">mlnf
<span class="token function">cd</span> /camp/home/ziffo/home/nextflow/nf-core/scrnaseq
sbatch -N 1 -c 4 --mem<span class="token operator">=</span>32G -t 48:00:00 --wrap<span class="token operator">=</span><span class="token string">"nextflow run nf-core/scrnaseq \
	--reads '/camp/stp/babs/outputs/gandhi-patani/doaa.taha/fastq_pooled/**/*_R{1,2}_001.fastq.gz' \
	--fasta '/camp/home/ziffo/home/genomes/sequences/human/Homo_sapiens.GRCh38.dna.alt.fa' \
	--gtf '/camp/home/ziffo/home/genomes/annotation/Homo_sapiens.GRCh38.99.gtf' \
	--transcriptome_fasta '/camp/home/ziffo/home/genomes/index/kallisto/Homo_sapiens.GRCh38.cdna.all.fa' \
	--save_reference \
	--email oliver.ziff@crick.ac.uk \
	-profile crick"</span> --mail-type<span class="token operator">=</span>ALL,ARRAY_TASKS --mail-user<span class="token operator">=</span>oliver.ziff@crick.ac.uk
</code></pre>
<pre><code>-with-singularity /camp/home/ziffo/home/nextflow/nf-core/scrnaseq/work/singularity/nfcore-scrnaseq-1.0.0.img
</code></pre>
<h2 id="container">Container</h2>
<pre class=" language-bash"><code class="prism  language-bash">singularity pull --name nfcore-scrnaseq-1.0.0.img docker://nfcore/scrnaseq:1.0.0
</code></pre>
<h2 id="profiles">Profiles</h2>
<p><code>-profile</code></p>
<p>nextflow run <a href="http://main-test.nf">main-test.nf</a> -resume</p>
<h2 id="configuration">Configuration</h2>
<p>crick profile config<br>
<a href="https://github.com/nf-core/configs/blob/master/conf/crick.config">https://github.com/nf-core/configs/blob/master/conf/crick.config</a></p>
<h2 id="debugging">Debugging</h2>
<p><code>more .nextflow.log</code><br>
more <code>.command.sh</code></p>
<h1 id="nextflow-course">Nextflow course</h1>
<p>March 2020</p>
<h1 id="write-script">Write Script</h1>
<p>Define processes within the pipeline.</p>
<pre class=" language-bash"><code class="prism  language-bash">  
params.str <span class="token operator">=</span> <span class="token string">'Hello world!'</span>

process splitLetters <span class="token punctuation">{</span>

    output:
    <span class="token function">file</span> <span class="token string">'chunk_*'</span> into letters

    <span class="token string">""</span><span class="token string">"
 printf '<span class="token variable">${params.str}</span>' | split -b 6 - chunk_
 "</span><span class="token string">""</span>
<span class="token punctuation">}</span>

process convertToUpper <span class="token punctuation">{</span>

    input:
    <span class="token function">file</span> x from letters.flatten<span class="token punctuation">(</span><span class="token punctuation">)</span>

    output:
    stdout result

    <span class="token string">""</span><span class="token string">"
 cat <span class="token variable">$x</span> | tr '[a-z]' '[A-Z]'
 "</span><span class="token string">""</span>
<span class="token punctuation">}</span>

result.view <span class="token punctuation">{</span> it.trim<span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">}</span>
</code></pre>
<p>Save script as <code>filename.nf</code></p>
<h1 id="execute-script">Execute Script</h1>
<p><code>nextflow run filename.nf</code></p>
<p>It will output something similar to:</p>
<pre class=" language-bash"><code class="prism  language-bash">N E X T F L O W  ~  version 19.04.0
executor <span class="token operator">&gt;</span>  local <span class="token punctuation">(</span>3<span class="token punctuation">)</span>
<span class="token punctuation">[</span>69/c8ea4a<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> splitLetters   <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 1 of 1 ✔
<span class="token punctuation">[</span>84/c8b7f1<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> convertToUpper <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 2 of 2 ✔
HELLO
WORLD<span class="token operator">!</span>
</code></pre>
<p>Processes are executed in parallel where possible.<br>
The [69/c8ea4a] numbers refer to each unique process. These are the prefix of the directories where each process is execues. You can inspect them in <code>$PWD/work</code>.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">## RUN PIPELINE  </span>
nextflow run nf-core/atacseq \  
-r 1.1.0 \  
--input <span class="token string">"design-atac.csv"</span> \  
-profile crick \  
-with-singularity /camp/apps/misc/stp/babs/nf-core/singularity/atacseq/1.1.0/nfcore-atacseq-1.1.0.img \  
--email <span class="token punctuation">[</span>oliver.ziff@crick.ac.uk<span class="token punctuation">]</span><span class="token punctuation">(</span>mailto:chris.cheshire@crick.ac.uk<span class="token punctuation">)</span> \  
--genome BDGP6 \  
--save_reference \  
-resume  
</code></pre>
<h1 id="modify-and-resume">Modify and Resume</h1>
<p>Only processes that are changed are re-excecuted. For processes not changed, the process cached result is used which saves time rerunning the whole pipeline.</p>
<p>After editing re-execute the script with:<br>
<code>nextflow run tutorial.nf -resume</code></p>
<p>It will print output similar to this:</p>
<pre class=" language-bash"><code class="prism  language-bash">N E X T F L O W  ~  version 19.04.0
executor <span class="token operator">&gt;</span>  local <span class="token punctuation">(</span>2<span class="token punctuation">)</span>
<span class="token punctuation">[</span>69/c8ea4a<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> splitLetters   <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 1 of 1, cached: 1 ✔
<span class="token punctuation">[</span>d0/e94f07<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> convertToUpper <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 2 of 2 ✔
olleH
<span class="token operator">!</span>dlrow
</code></pre>
<p><code>$PWD/work</code> caches all results which takes up a lot of space - clean the folder regularly.</p>
<h1 id="pipeline-parameters">Pipeline parameters</h1>
<p>Set parameters with <code>--</code> then <code>parameter name</code> then <code>parameter</code><br>
<code>nextflow run tutorial.nf --str 'Bonjour le monde'</code></p>
<h1 id="configuration-file">Configuration file</h1>
<p><code>nextflow.config</code> file needs to be in the pipeline execution directory. The file defines which executor to use; environmental variables; pipeline parameters.</p>
<p><a href="https://crickcomputingchat.slack.com/archives/DQQ77NYJW/p1581442198001000">5:29</a></p>
<h2 id="scrnaseq-nfcore-pipeline">scRNAseq nfcore pipeline</h2>
<p><a href="https://nf-co.re/scrnaseq">https://nf-co.re/scrnaseq</a></p>
<h2 id="update-pipline">UPDATE PIPLINE</h2>
<p>nextflow pull nf-core/atacseq</p>
<h1 id="course">Course</h1>
<p>Course notes for DAY 1 <a href="https://www.seqera.io/training/day1/index.html">https://www.seqera.io/training/day1/index.html</a><br>
2nd March 2020</p>
<p>ssh <a href="mailto:ubuntu@ec2-34-253-38-90.eu-west-1.compute.amazonaws.com">ubuntu@ec2-34-253-38-90.eu-west-1.compute.amazonaws.com</a></p>
<p>Singularity<br>
Docker containers<br>
Exercutors: cloud or own infrastructure<br>
Error recovery: resume jobs<br>
New features with DSL 2.0: modular components</p>
<h2 id="workflows">Workflows</h2>
<p>Automation - push the button pipeline<br>
embarrassingly parellelisation: spawn 100s of jobs over distributed cluster<br>
mash up different tools &amp; scripts (dependencies)<br>
dependency trees can be very complex: processes (circles) connected by arrows. E.g. 70 process tasks.<br>
Reproducability in computational biology - different libraries, different softwares &amp; execution environment, rounding errors<br>
Challenges: reproducible; portable; scalable; usable; traceable.</p>
<p>Fundamentals for scaleable workflows:<br>
takes code in any language: R, bash<br>
define software depndecies: conda, docker<br>
define dataflow programme<br>
version control: github<br>
deploy in a cloud: google cloud, aws, platform computing</p>
<p>Fast protocotyping: custom DSL. Enables tasks compositions. Written in Groovy (like python for java).<br>
Easy parallelisation: what is in channels<br>
Self contained: each task is independent- can run separate sections of different environments.<br>
Portable deployments</p>
<h3 id="example">Example:</h3>
<p>Input:<br>
file ref.fa from genome_channel<br>
file sample.fq from reads_ch</p>
<p>output:<br>
file sample.bam into bam_ch</p>
<p>script:<br>
“”"<br>
bwa mem ref.fa sample.fq | samtools sort -o sample.bam<br>
“”"</p>
<p>output of one process is the input of the next process.</p>
<h2 id="dataflow">Dataflow</h2>
<p>input &gt; splitting &gt; tasks / map / task &gt; filter &gt; collect ? result<br>
channel = contains elements<br>
process =execute for each element. each exectuion = task.</p>
<p>implicit parallelism: will run process as many times as many files are delivered to it (with wildcard *)</p>
<p><code>Channel.fromFilePairs("*_{1,2}.fq")</code><br>
<code>( iPSC, [ipsc_1.fq, ipsc_2.fq])</code></p>
<h2 id="deployment-scenarios">Deployment scenarios</h2>
<p>local execution: nextflow - docker/singularity container - OS - local storage<br>
centralised orchestration: nextflow command &gt; each task becomes a cluster job.<br>
distributed orchestration: single job to cluster, but nextflow will split jobs are required.</p>
<p>process {<br>
executor<br>
queue</p>
<h2 id="containers">Containers</h2>
<p>Docker or Singularity (also Podman)<br>
faster startup. lighter.<br>
layers. can be built upon.<br>
transparent - see how they are built<br>
always use containers. makes things much easier.</p>
<h2 id="execution-reports">Execution reports</h2>
<p>CPU usage, time &gt;&gt; optimise pipeline<br>
QC look at tasks. shows what takes longer.<br>
DAG visualisation: shows the pipeline ordering.<br>
Text editors: Atom, <strong>Visual studio code</strong> - has ssh client &amp; nextflow highlight &amp; docker</p>
<h2 id="nf-core-1">nf-core</h2>
<p>community effort to collect production ready analysis pipelines built with nextflow</p>
<h1 id="directories">Directories</h1>
<p>Files are saved to directories for each task. the naming of each directory is noted in the nextflow output on terminal<br>
Sub tasks filed are put into sub directories.</p>
<h1 id="debug">Debug</h1>
<p>errors can be investigated by going to the work directory that has failed. get the directory ID from the nextflow output.<br>
<code>more .command.sh</code></p>
<p><code>nextflow run script.nf</code></p>
<pre class=" language-bash"><code class="prism  language-bash">ubuntu@ip-172-31-40-29:~/nf-course/day1$ nextflow run simple.nf
N E X T F L O W  ~  version 20.01.0
Launching <span class="token variable"><span class="token variable">`</span>simple.nf<span class="token variable">`</span></span> <span class="token punctuation">[</span>agitated_cantor<span class="token punctuation">]</span> - revision: d5b8b09fe6
executor <span class="token operator">&gt;</span>  local <span class="token punctuation">(</span>3<span class="token punctuation">)</span>
<span class="token punctuation">[</span>a8/4a74ce<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> splitLetters   <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 1 of 1 ✔
<span class="token punctuation">[</span>81/bb9bde<span class="token punctuation">]</span> process <span class="token operator">&gt;</span> convertToUpper <span class="token punctuation">[</span>100%<span class="token punctuation">]</span> 2 of 2 ✔
olleH
<span class="token operator">!</span>dlrow
</code></pre>
<p>The working directories are <code>a8/4a74ce</code> and <code>81/bb9bde</code></p>
<h2 id="view">View</h2>
<p><code>letters.flatten().view()</code>at the end of script shows directory structure. Helpfil to debug.View content of channel.</p>
<h2 id="dump">Dump</h2>
<p><code>letters.flatten().dump()</code> prints contents of all channels</p>
<h1 id="syntax">Syntax</h1>
<p>Nextflow scripts are written in Groovy language (like python). <a href="http://docs.groovy-lang.org/latest/html/groovy-jdk/java/util/List.html">http://docs.groovy-lang.org/latest/html/groovy-jdk/java/util/List.html</a><br>
println.<br>
comments // (not #)<br>
assign variables with <code>=</code> e.g. x = 1<br>
define local variables with <code>def</code><br>
lists are with <code>[ list ]</code> list = [10,20,30]<br>
access element of list with [ ] or list.get(0) # first element is 0 with groovy<br>
maps are lists with any character (not just integers).<br>
<code>map = [a:0, b:1, c:2]</code><br>
strings defined in single or double quotes<br>
multiline string with tripple double quote</p>
<pre class=" language-groovy"><code class="prism  language-groovy"><span class="token string gstring">"""
Hello
Here here
"""</span>
</code></pre>
<p>if statement:</p>
<pre class=" language-groovy"><code class="prism  language-groovy">
<span class="token keyword">if</span><span class="token punctuation">(</span> <span class="token operator">&lt;</span> statement <span class="token operator">&gt;</span> <span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token comment">//true branch</span>
<span class="token punctuation">}</span>
<span class="token keyword">else</span> <span class="token punctuation">{</span>
<span class="token comment">//false branch</span>
<span class="token punctuation">}</span>

<span class="token keyword">if</span> list<span class="token punctuation">:</span>
<span class="token keyword">if</span><span class="token punctuation">(</span> <span class="token operator">&lt;</span> statement <span class="token operator">&gt;</span> <span class="token punctuation">)</span> <span class="token punctuation">{</span>
	println list
<span class="token punctuation">}</span>
<span class="token keyword">else</span> <span class="token punctuation">{</span>
	println <span class="token string">'THe list is empty'</span>
<span class="token punctuation">}</span>
<span class="token function">println</span><span class="token punctuation">(</span> list <span class="token operator">?:</span> <span class="token string gstring">"The list is empty"</span> <span class="token punctuation">)</span>
</code></pre>
<p>for loop syntax</p>
<pre class=" language-groovy"><code class="prism  language-groovy"><span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span><span class="token number">3</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
   <span class="token function">println</span><span class="token punctuation">(</span><span class="token string gstring">"Hello World <span class="token expression"><span class="token punctuation">$</span>i</span>"</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Closures</strong>: define a function like a variable.<br>
<code>square = { it * it }</code> square function<br>
<code>println square.call(5)</code> prints the result of 5 * 5</p>
<p><code>list = [1,2,3] list.each { println (it * it) }</code><br>
<code>z = list.collect { it * it) }</code> # saves result of closure to variable z</p>
<h1 id="modifying-scripts">Modifying scripts</h1>
<p><code>nextflow run script.nf -resume</code><br>
avoids rerunning the whole script from scratch</p>
<h2 id="define-parameters-at-start">Define parameters at start</h2>
<p>params.greeting = “Hello World”<br>
params.outdir = “some/path”</p>
<p>Change greeting: <code>nextflow run simple.nf --greeting "Hola Mundo"</code><br>
note <code>--</code> not <code>-</code> greeting.<br>
<code>-resume</code> only needs 1 <code>-</code></p>
<h2 id="define-channels-at-start">Define channels at start</h2>
<p>greeting_ch = Channe;.from ()</p>
<h1 id="channels">Channels</h1>
<p>Channels logically connect taks to each other.<br>
Doesnt need to wait for implementation of other tasks. The task will run as soon as the input files are available.<br>
Only 1 task can consume a channel.</p>
<p>2 types of channels:</p>
<ol>
<li>Queue = asynchronous unidirectional FIFO connecting 2 processing</li>
<li>Value = singleton channel. Bound to single value. Can be read unlimited times. Not consumed.</li>
</ol>
<h2 id="frompath">fromPath</h2>
<p>creates a queue channel.<br>
Uses wildcard to take in all files!</p>
<pre class=" language-groovy"><code class="prism  language-groovy">ch <span class="token operator">=</span> Channel<span class="token operator">.</span><span class="token function">fromPath</span><span class="token punctuation">(</span><span class="token string">'data/ggal/*.fq'</span><span class="token punctuation">)</span>
ch<span class="token operator">.</span><span class="token function">view</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>Can take in all subdirectories:</p>
<pre class=" language-groovy"><code class="prism  language-groovy">ch <span class="token operator">=</span> Channel<span class="token operator">.</span><span class="token function">fromPath</span><span class="token punctuation">(</span><span class="token string">'data/**/*.fq'</span><span class="token punctuation">)</span>
ch<span class="token operator">.</span><span class="token function">view</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>fromFilePairs handles pairs of files:</p>
<pre class=" language-groovy"><code class="prism  language-groovy">Channel
    <span class="token operator">.</span><span class="token function">fromFilePairs</span><span class="token punctuation">(</span><span class="token string">'/my/data/SRR*_{1,2}.fastq'</span><span class="token punctuation">)</span>
    <span class="token operator">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p><code>{1,2}</code> wildcard takes files ending 1 or 2.</p>
<pre class=" language-groovy"><code class="prism  language-groovy">Channel
<span class="token operator">.</span><span class="token function">fromFilePairs</span><span class="token punctuation">(</span><span class="token string">'data/ggal/*_{1,2}.fq'</span><span class="token punctuation">)</span>
<span class="token operator">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<h2 id="fromsra">fromSRA</h2>
<p>returns all fastq files from a single SRA ID</p>
<pre class=" language-groovy"><code class="prism  language-groovy">Channel
<span class="token operator">.</span><span class="token function">fromSRA</span><span class="token punctuation">(</span><span class="token string">'SRP043510'</span><span class="token punctuation">)</span>
<span class="token operator">.</span><span class="token function">view</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<h1 id="processes">Processes</h1>
<p>Script block is the command that runs the process. Each process has 1 script block.<br>
Default is bash script but scan change to python, R eg</p>
<pre class=" language-groovy"><code class="prism  language-groovy">process example <span class="token punctuation">{</span>
    script<span class="token punctuation">:</span>
    <span class="token string gstring">"""
    blastp -db /data/blast -query query.fa -outfmt 6 &gt; blast_result
    cat blast_result | head -n 10 | cut -f 2 &gt; top_hits
    blastdbcmd -db /data/blast -entry_batch top_hits &gt; sequences
    """</span>
<span class="token punctuation">}</span>


process pyStuff <span class="token punctuation">{</span>
  script<span class="token punctuation">:</span>
  <span class="token string gstring">"""
  #!/usr/bin/env python

  x = 'Hello'
  y = 'world!'
  print "%s - %s" % (x,y)
  """</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="verbose-using-echo">Verbose using echo</h2>
<p>echo true in script<br>
or add -process.echo true</p>
<h2 id="escape-bash-variables">escape bash variables</h2>
<p>set bash variables e.g. FASTQ = .fastq<br>
in script escape with <code>\</code></p>
<pre><code>  """
X = 1
echo Hello \$X
  """
}
</code></pre>
<h1 id="conditional-script">Conditional Script</h1>
<p>Default aligner is kallisto, but can change aligner to salmon with <code>--aligner= 'salmon'</code></p>
<pre class=" language-groovy"><code class="prism  language-groovy">params<span class="token operator">.</span>aligner <span class="token operator">=</span> <span class="token string">'kallisto'</span>

process foo <span class="token punctuation">{</span>
  script<span class="token punctuation">:</span>
  <span class="token keyword">if</span><span class="token punctuation">(</span> params<span class="token operator">.</span>aligner <span class="token operator">==</span> <span class="token string">'kallisto'</span> <span class="token punctuation">)</span>
    <span class="token string gstring">"""
    kallisto --reads /some/data.fastq
    """</span>
  <span class="token keyword">else</span> <span class="token keyword">if</span><span class="token punctuation">(</span> params<span class="token operator">.</span>aligner <span class="token operator">==</span> <span class="token string">'salmon'</span> <span class="token punctuation">)</span>
    <span class="token string gstring">"""
    salmon --reads /some/data.fastq
    """</span>
  <span class="token keyword">else</span>
    <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token string gstring">"Unknown aligner <span class="token expression"><span class="token punctuation">$</span>params<span class="token operator">.</span>aligner</span>"</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<h1 id="input">Input</h1>
<p>syntax:</p>
<pre><code>input: 
	&lt;input qualifier&gt; &lt;input name&gt; from &lt;source channel&gt;
</code></pre>
<p>val is the inpur qualifier</p>
<pre><code>num = Channel.from( 1, 2, 3 )

process basicExample {
  input:
  val x from num

  """
  echo process job $x
  """
}
</code></pre>
<pre><code>reads = Channel.fromPath( 'data/ggal/*.fq' )
process foo {
input:
file 'sample.fastq' from reads
script:
"""
echo your_command --reads sample.fastq
"""
}
</code></pre>
<p>See the links to these files in work directory:<br>
<code>tree work/</code></p>
<h1 id="output">Output</h1>
<p>With tuple can use <code>sample_id</code> which contains the metadata &amp; actual data.</p>
<h1 id="write-a-pipeline">Write a pipeline</h1>
<p>4 processes:<br>
Index a transcritome file<br>
QC fastqc<br>
quantification<br>
MultQC report</p>
<p>Index</p>
<pre><code>params.reads = "$baseDir/data/ggal/*_{1,2}.fq"
params.transcriptome = "$baseDir/data/ggal/transcriptome.fa"
params.multiqc = "$baseDir/multiqc"
println "reads: $params.reads"
</code></pre>
<p>nexflow run <a href="http://script1.nf">script1.nf</a> --reads</p>
<h1 id="log">Log</h1>
<p><code>cat .nextflow.log</code></p>
<h1 id="containers-have-software">Containers have software</h1>
<p><code>nextflow run script2.nf -with-docker</code><br>
-with-docker brings in the container from the config:</p>
<h2 id="nextflow.config">nextflow.config</h2>
<pre class=" language-groovy"><code class="prism  language-groovy">process<span class="token operator">.</span>container <span class="token operator">=</span> <span class="token string">'nextflow/rnaseq-nf'</span>
docker<span class="token operator">.</span>runOptions<span class="token operator">=</span><span class="token string">'-u $(id -u):$(id -g)'</span>
</code></pre>
<p>add to config to mean <code>-with-docker</code> not needed every time<br>
<code>docker.enabled = true</code></p>
<p><code>tree work/</code> shows all files in directories in work/<br>
<code>$task.cpus</code> can be specified outside of the script. Makes it easy to change CPUs.</p>
<p><code>.collect</code> will group all files into a single working directory to run into a single channel<br>
<code>.mix</code> will combine channels</p>
<h1 id="long-r-scripts">Long R scripts</h1>
<p>mkdir bin/<br>
add to bin the long R script<br>
chmod +x <a href="http://script.sh">script.sh</a> # make executable<br>
nextflow will auto add bin to path<br>
can remove script from nextflow &amp; call external R script - give input.</p>
<h1 id="github">GitHub</h1>
<p>repository</p>
<p>data/<br>
docker<br>
nextflow.config<br>
<a href="http://main.nf">main.nf</a></p>
<p><code>nextflow run nextflow-io/rnaseq-nf -with-docker</code> # pulls the code &amp; docker</p>
<h1 id="specify-revision--version">Specify revision / version</h1>
<p><code>nextflow run -r v1.2</code><br>
can be a version, revision, commit ID.</p>
<h1 id="dependencies">Dependencies</h1>
<h2 id="docker">Docker</h2>
<p>Docker client is a service that runs in the background. Finds image by name. If not on local machine it can pull it from remote Docker hub &gt;&gt; creates a container from the image &gt;&gt; runs command<br>
NB security concerns with docker permissions</p>
<pre><code>docker run -it debian:jessie
</code></pre>
<p>changes directory to the container<br>
<code>exit</code> to return to previous directory</p>
<h2 id="dockerfile">Dockerfile</h2>
<p>Script to create image</p>
<pre class=" language-groovy"><code class="prism  language-groovy">FROM debian<span class="token punctuation">:</span>jessie

MAINTAINER <span class="token operator">&lt;</span>your name<span class="token operator">&gt;</span>

RUN apt<span class="token operator">-</span>get update <span class="token operator">&amp;&amp;</span> apt<span class="token operator">-</span>get install <span class="token operator">-</span>y curl cowsay

ENV PATH<span class="token operator">=</span><span class="token punctuation">$</span>PATH<span class="token punctuation">:</span><span class="token string regex">/usr/</span>games<span class="token operator">/</span>

RUN curl <span class="token operator">-</span>sSL https<span class="token punctuation">:</span><span class="token string regex">//</span>github<span class="token operator">.</span>com<span class="token string regex">/COMBINE-lab/</span>salmon<span class="token string regex">/releases/</span>download<span class="token string regex">/v1.0.0/</span>salmon<span class="token operator">-</span><span class="token number">1.0</span><span class="token operator">.</span>0_linux_x86_64<span class="token operator">.</span>tar<span class="token operator">.</span>gz <span class="token operator">|</span> tar xz \
 <span class="token operator">&amp;&amp;</span> mv <span class="token string regex">/salmon-*/</span>bin<span class="token comment">/* /usr/bin/ \
 &amp;&amp; mv /salmon-*/</span>lib<span class="token comment">/* /usr/lib/
</span></code></pre>
<p>Aways build containers in empty directories.<br>
<code>mkdir docker code Dockerfile</code><br>
tag image default is latest: Successfully tagged my-image:latest</p>
<p>Build the docker container:<br>
<code>docker build -t my-image .</code></p>
<p>Run interactively inside of container:</p>
<pre><code>docker run -it my-image bash
</code></pre>
<p>Use container software &amp; files to run a command. All files to run with container need to be accessible - mount the current file system into the container:</p>
<pre><code>docker run --volume $HOME:$HOME --workdir -u $(id -u):$(id -g) $PWD my-image  salmon index -t $PWD/data/ggal/transcriptome.fa -i transcript-index 
</code></pre>
<p><code>-u $(id -u):$(id -g)</code></p>
<h1 id="docker-hub">Docker hub</h1>
<p>publish container publically to docker hub account: tag it with Docker username, then push</p>
<pre><code>docker tag my-image &lt;user-name&gt;/my-image

docker push &lt;user-name&gt;/my-image
</code></pre>
<p>download a container with pull</p>
<pre><code>docker pull &lt;user-name&gt;/my-image
</code></pre>
<h2 id="docker-hub-with-nextflow">Docker hub with nextflow</h2>
<pre><code>nextflow run script2.nf -with-docker my-image
</code></pre>
<p>See the nextflow commands that were run:</p>
<pre><code>cd /work/nextflow_ID_directory/
more .command.run
</code></pre>
<h1 id="singularity">Singularity</h1>
<p>secure version of Docker for use on HPCs. permissions are from user - not from root.</p>
<h2 id="singularity-file">Singularity file:</h2>
<pre><code>Bootstrap: docker
From: debian:jessie

%environment
export PATH=$PATH:/usr/games/

%labels
AUTHOR &lt;your name&gt;

%post

apt-get update &amp;&amp; apt-get install -y locales-all curl cowsay
curl -sSL https://github.com/COMBINE-lab/salmon/releases/download/v1.0.0/salmon-1.0.0_linux_x86_64.tar.gz | tar xz \
 &amp;&amp; mv /salmon-*/bin/* /usr/bin/ \
 &amp;&amp; mv /salmon-*/lib/* /usr/lib/
</code></pre>
<p>Run file with:</p>
<pre><code>sudo singularity build my-image.sif Singularity
</code></pre>
<p>Run container:</p>
<pre><code>singularity exec my-image.sif cowsay 'Hello Singularity'
</code></pre>
<p>Run interactively from inside container:</p>
<pre><code>singularity shell my-image.sif
</code></pre>
<p><code>exit</code> to return to previous directory</p>
<p>best to write images in docker rather than singularity. Singularity will format docker files automatically:</p>
<pre><code>singularity pull docker://debian:jessie
</code></pre>
<p>run with nextflow</p>
<pre><code>nextflow run script7.nf -with-singularity nextflow/rnaseq-nf
</code></pre>
<p>this creates a singularity directory if not specified</p>
<h2 id="singularity-library-sylabs">Singularity library: SyLabs</h2>
<p><a href="https://sylabs.io/">https://sylabs.io/</a><br>
<a href="https://cloud.sylabs.io/home">https://cloud.sylabs.io/home</a></p>
<h1 id="condabioconda">Conda/Bioconda</h1>
<p>Conda environment is defined in a YAML file:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment"># CONDA ENVIRONMENT FILE </span>
name: nf-tutorial
channels:
  - defaults
  - bioconda
  - conda-forge
dependencies:
  <span class="token comment"># Default bismark</span>
  - salmon<span class="token operator">=</span>0.8.2
  - fastqc<span class="token operator">=</span>0.11.5
  - multiqc<span class="token operator">=</span>1.5
</code></pre>
<p>create conda envrionment:</p>
<pre><code>conda env create --file env.yml
</code></pre>
<p>activate conda environment:</p>
<pre><code>conda activate nf-tutorial
</code></pre>
<p>run nextflow with conda:</p>
<pre><code>nextflow run script7.nf -with-conda /home/ubuntu/miniconda2/envs/nf-tutorial
</code></pre>
<p>Best to put the conda environment inside the Dockerfile with COPY conda.yml:</p>
<pre><code>FROM continuumio/miniconda:4.7.12

MAINTAINER Paolo Di Tommaso &lt;paolo.ditommaso@gmail.com&gt;

RUN apt-get -y install ttf-dejavu

COPY conda.yml .
RUN \
conda env update -n root -f conda.yml \
&amp;&amp; conda clean -a

RUN apt-get install -y procps
</code></pre>
<p>This docker image has miniconda &gt;&gt; add conda environment</p>
<h1 id="nextflow-configuration">Nextflow Configuration</h1>
<p>Nextflow looks for <code>nextflow.config</code> in:</p>
<ol>
<li>current directory</li>
<li>then in script base directory</li>
<li>then <code>$HOME/.nextflow/config</code></li>
</ol>
<h2 id="config-syntax">Config Syntax</h2>
<pre><code>propertyOne = 'world'
anotherProp = "Hello $propertyOne"
customPath = "$PATH:/my/app/folder"
</code></pre>
<p>access any variable defined in the host environment such as <code>$PATH</code>, <code>$HOME</code>, <code>$PWD</code>, etc.</p>
<p>Add Comments:</p>
<pre><code>// comment a single config file

/*
   a comment spanning
   multiple lines
 */
</code></pre>
<p>Define properties either  with dot prefix:<br>
alpha.x  = 1<br>
alpha.y  = ‘string value…’</p>
<p>Or group together:<br>
beta {<br>
p = 2<br>
q = ‘another string …’<br>
}</p>
<p>Define params:</p>
<pre><code>// workflow script
params.foo = 'Hello'
params.bar = 'world!'

// print the both params
println "$params.foo $params.bar"
</code></pre>
<p>Can change params from the command line eg change foo to Hola</p>
<pre><code>nextflow run params.nf --foo Hola
</code></pre>
<h2 id="config-env">Config env</h2>
<p>defines where workflow will be executed</p>
<pre><code>env.ALPHA = 'some value'
env.BETA = "$HOME/some/path"
</code></pre>
<h2 id="config-process">Config process</h2>
<p>settings for the task execution such as <code>cpus</code>, <code>memory</code>, <code>container</code> and other resources in the pipeline script:</p>
<pre><code>process {
    cpus = 10
    memory = 8.GB
    container = 'biocontainers/bamtools:v2.4.0_cv3'
}
</code></pre>
<p>better to define these in the config than the nextflow script</p>
<p>process { memory = { <a href="http://4.GB">4.GB</a> * task.cpus } }</p>
<h2 id="docker-execution">Docker execution</h2>
<p>specify container image:</p>
<pre><code>process.container = 'nextflow/rnaseq-nf@sha256:aeacbd7ea1154f263cda972a96920fb228b2033544c2641476350b9317dab266'
docker.enabled = true
</code></pre>
<p>or with sinuglarity</p>
<pre><code>process.container = '/some/singularity/image.sif'
singularity.enabled = true
</code></pre>
<p>Use singularity to pull docker image:</p>
<pre><code>process.container = 'nextflow/rnaseq-nf'
singularity.enabled = true
</code></pre>
<p>following protocols are supported:</p>
<ul>
<li><code>library://</code>  download the container image from the  <a href="https://cloud.sylabs.io/library">Singularity Library service</a></li>
<li><code>shub://</code>  download the container image from the  <a href="https://singularity-hub.org/">Singularity Hub</a>.</li>
<li><code>docker://</code>  download the container image from the  <a href="https://hub.docker.com/">Docker Hub</a>  and convert it to the Singularity format.</li>
<li><code>docker-daemon://</code>  pull the container image from a local Docker installation and convert it to a Singularity image file.</li>
</ul>
<h2 id="conda-execution">Conda execution</h2>
<p>a Conda environment can be provided in the configuration file adding the following setting in  <code>nextflow.config</code>:</p>
<pre><code>process.conda = "/home/ubuntu/miniconda2/envs/nf-tutorial"
</code></pre>
<p>path is to either the conda directory or the YAML file</p>
<h1 id="deployment-on-hpc">Deployment on HPC</h1>
<p>To run your pipeline with a batch scheduler modify the  <code>nextflow.config</code>  file specifying the target executor and the required computing resources if needed. For example:</p>
<pre><code>process.executor = 'slurm'
</code></pre>
<p>specify the amount of resources i.e. cpus, memory, execution time, etc. required by each task. Use the scope  <code>process</code>  to define the resource requirements for all processes in your workflow applications. For example:</p>
<pre><code>process {
    executor = 'slurm'
    queue = 'short'
    memory = '10 GB'
    time = '30 min'
    cpus = 4
}
</code></pre>
<p>Different tasks need different amount of computing resources. It is possible to define the resources for a specific task using the select <code>withName:</code> followed by the process name:</p>
<p>process {<br>
executor = ‘slurm’<br>
queue = ‘short’<br>
memory = ‘10 GB’<br>
time = ‘30 min’<br>
cpus = 4</p>
<pre><code>withName: foo {
    cpus = 4
    memory = '20 GB'
    queue = 'short'
}

withName: bar {
    cpus = 8
    memory = '32 GB'
    queue = 'long'
}
</code></pre>
<p>}</p>
<h2 id="label-processes">Label processes</h2>
<p>When a workflow is composed by many processes it can be arduous listing all process names in the config to specify the resources for each of them. Better to annotate the processes with a  <a href="https://www.nextflow.io/docs/latest/process.html#label">label</a> . Then specify the resources in the config using for all processes having the same label.</p>
<p>workflow script:</p>
<pre><code>process task1 {
  label 'long'

  """
  first_command --here
  """
}

process task2 {
  label 'short'

  """
  second_command --here
  """
}
</code></pre>
<p>config:</p>
<pre><code>process {
    executor = 'slurm'

    withLabel: 'short' {
        cpus = 4
        memory = '20 GB'
        queue = 'alpha'
    }

    withLabel: 'long' {
        cpus = 8
        memory = '32 GB'
        queue = 'omega'
    }
}
</code></pre>
<h2 id="configure-multiple-containers-in-a-single-workflow">Configure multiple containers in a single workflow</h2>
<p>different container for each process in your workflow:</p>
<pre><code>process {
  withName: foo {
    container = 'some/image:x'
  }
  withName: bar {
    container = 'other/image:y'
  }
}

docker.enabled = true
</code></pre>
<h2 id="config-profiles">Config profiles</h2>
<p>Configuration files can contain the definition of one or more  <em>profiles</em>. A profile is a set of configuration attributes that can be activated/chosen when launching a pipeline execution by using the  <code>-profile</code>  command line option. Configuration profiles are defined by using the special scope  <code>profiles</code>  which group the attributes that belong to the same profile using a common prefix. For example:</p>
<pre><code>profiles {

    standard {
        params.genome = '/local/path/ref.fasta'
        process.executor = 'local'
    }

    cluster {
        params.genome = '/data/stared/ref.fasta'
        process.executor = 'sge'
        process.queue = 'long'
        process.memory = '10GB'
        process.conda = '/some/path/env.yml'
    }

    cloud {
        params.genome = '/data/stared/ref.fasta'
        process.executor = 'awsbatch'
        process.container = 'cbcrg/imagex'
        docker.enabled = true
    }

}
</code></pre>
<p>set different process configuration strategies depending on the target runtime platform. By convention the <code>standard</code> profile is implicitly used when no other profile is specified by the user.</p>
<p>enable a specific profile use  <code>-profile</code>  option followed by the profile name:</p>
<pre><code>nextflow run &lt;your script&gt; -profile cluster
</code></pre>
<h1 id="organise-outputs">Organise Outputs</h1>
<p>Nextflow manages independently workflow execution intermediate results from the pipeline expected outputs. Task output files are created in the task specific execution directory which is considered as a temporary directory that can be deleted upon completion. The pipeline result files need to be marked explicitly using the directive <a href="https://www.nextflow.io/docs/latest/process.html#publishdir">publishDir</a>  in the process that’s creating such file. For example:</p>
<pre><code>process makeBams {
    publishDir "/some/directory/bam_files", mode: 'copy'

    input:
    file index from index_ch
    tuple val(name), file(reads) from reads_ch

    output:
    tuple val(name), file ('*.bam') into star_aligned

    """
    STAR --genomeDir $index --readFilesIn $reads
    """
}
</code></pre>
<p>this copies all bam files created by the <code>star</code> task into <code>/some/directory/bam_files</code></p>
<h3 id="subdirectories">Subdirectories</h3>
<p>use more then one <code>publishDir</code> to keep different outputs in separate directory</p>
<pre><code>params.reads = 'data/reads/*_{1,2}.fq.gz'
params.outdir = 'my-results'

Channel
    .fromFilePairs(params.reads, flat: true)
    .set{ samples_ch }

process foo {
  publishDir "$params.outdir/$sampleId/", pattern: '*.fq'
  publishDir "$params.outdir/$sampleId/counts", pattern: "*_counts.txt"
  publishDir "$params.outdir/$sampleId/outlooks", pattern: '*_outlook.txt'

  input:
    set sampleId, file('sample1.fq.gz'), file('sample2.fq.gz') from samples_ch
  output:
    file "*"
  script:
  """
    &lt; sample1.fq.gz zcat &gt; sample1.fq
    &lt; sample2.fq.gz zcat &gt; sample2.fq

    awk '{s++}END{print s/4}' sample1.fq &gt; sample1_counts.txt
    awk '{s++}END{print s/4}' sample2.fq &gt; sample2_counts.txt

    head -n 50 sample1.fq &gt; sample1_outlook.txt
    head -n 50 sample2.fq &gt; sample2_outlook.txt
  """
}
</code></pre>
<p>create an output structure in the directory <code>my-results</code>, which contains a separate sub-directory for each given sample ID each of which contain the folders <code>counts</code> and <code>outlooks</code></p>
<h1 id="cache">Cache</h1>
<p>Nextflow caching mechanism works assigning a unique ID to each task which is used to create a separate execution directory where the tasks are executed and the results stored. The task unique ID is generated as a 128-bit hash number obtained composing the task inputs values and files and the command string. View this structure with <code>tree work/</code></p>
<h2 id="resume">Resume</h2>
<p>The <code>-resume</code> command line option allow the continuation of a pipeline execution since the last step that was successfully completed:</p>
<pre><code>nextflow run &lt;script&gt; -resume
</code></pre>
<p>Nextflow uses the task unique ID to check if the work directory already exists and it contains a valid command exit status and the expected output files. If this condition is satisfied the task execution is skipped and previously computed results are used as the process results. The first task, for which a new output is computed, invalidates all downstream executions in the remaining DAG.</p>
<h2 id="work">Work/</h2>
<p>The task work directories are created in the folder <code>work/</code> in the launching path by default. This is supposed to be a <strong>scratch</strong> storage area that can be cleaned up once the computation is completed. The final output are supposed to the stored in a different location specified using one or more <a href="https://www.nextflow.io/docs/latest/process.html#publishdir">publishDir</a> directive.</p>
<p>A different location for the execution work directory can be specified using the command line option  <code>-w</code>  e.g.</p>
<pre><code>nextflow run &lt;script&gt; -w /some/scratch/dir
</code></pre>

