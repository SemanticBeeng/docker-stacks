# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM jupyter/scipy-notebook

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Install Tensorflow
RUN /bin/bash -c "source activate yieldsdev_env && \
	\
	conda install --quiet --yes \
    'tensorflow=1.5*' \
    'keras=2.0.9*' && \
    conda clean -tipsy && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER"
