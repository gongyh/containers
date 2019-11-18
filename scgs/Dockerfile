FROM continuumio/miniconda3:4.5.4
LABEL authors="Yanhai Gong" \
      description="Docker image containing all requirements for gongyh/nf-core-scgs pipeline"

# Install procps so that Nextflow can poll CPU usage
RUN apt-get update && apt-get install -y procps libpng16-16 cmake gcc g++ hmmer2 && apt-get clean -y 

# Install locale en_US.UTF-8 used by Picard
RUN apt-get install -y locales && sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen && locale-gen

# Install resfinder and pointFinder
RUN cd /opt && git clone https://git@bitbucket.org/genomicepidemiology/resfinder.git
RUN cd /opt && git clone https://bitbucket.org/genomicepidemiology/pointfinder.git

# Keep a copy of current pipeline to container
COPY . /opt/nf-core-scgs/

# Install conda environments
COPY environment.yml /
RUN conda install python=3.6 conda=4.6.12 && conda env update -n base -f /environment.yml && conda clean -a

# Install Bioconductor packages
RUN R -e "install.packages('BiocManager', repos='https://cloud.r-project.org'); BiocManager::install('GenomeInfoDbData'); BiocManager::install('AneuFinder')"

# Download silva and busco for Quast 5.x, update taxa db for krona
RUN quast-download-silva && ktUpdateTaxonomy.sh #&& quast-download-busco
