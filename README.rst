=========================
Sphinx to GitHub Pages V2
=========================

.. image:: https://img.shields.io/github/stars/sphinx-notes/pages.svg?style=social&label=Star&maxAge=2592000
   :target: https://github.com/sphinx-notes/pages

Help you deploying your Sphinx documentation to Github Pages.


Fork notes
==========

Modified Github action ``sphinx-notes/pages@v2`` which publish built docs in
branch named folders.

- create ``docs/`` directory with ``sphinx-quickstart``, enable ``sphinx.ext.githubpages``
- prepare clean ``gh-pages`` branch

.. code-block:: console

   $ git checkout --orphan gh-pages
   $ rm -rf *
   $ touch .nojekyll
   $ git add .nojekyll
   $ git commit -v -a -m 'init gh-pages'
   $ git push origin gh-pages

- add pages.yaml workflow (see Usage)


Usage
=====

.. note::

   You should enable extension ``sphinx.ext.githubpages`` in your ``conf.py``
   first.

This action only help you build and commit Sphinx documentation to ``gh-pages``,
branch. So we need some other actions:

- ``action/setup-python`` for installing python and pip
- ``actions/checkout`` for checking out git repository
- ``ad-m/github-push-action`` for pushing site to remote

So your workflow file should be:

.. code-block:: yaml

   name: Pages
   on:
     push:
       branches:
       - master
   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
       - uses: actions/setup-python@v2
       - uses: actions/checkout@master
         with:
           fetch-depth: 0 # otherwise, you will failed to push refs to dest repo
       - name: Build and Commit
         uses: bodik/sphinx-notes-pages@v2
       - name: Push changes
         uses: ad-m/github-push-action@master
         with:
           github_token: ${{ secrets.GITHUB_TOKEN }}
           branch: gh-pages


Inputs
======

======================= ============== ============ =============================
Input                   Default        Required     Description
----------------------- -------------- ------------ -----------------------------
``documentation_path``  ``'./docs'``   ``false``    Relative path under
                                                    repository to documentation
                                                    source files
``target_branch``       ``'gh-pages'`` ``false``    Git branch where assets will
                                                    be deployed
``repository_path``     ``'.'``        ``false``    Relative path under
                                                    ``$GITHUB_WORKSPACE`` to
                                                    place the repository.
                                                    You not need to set this
                                                    Input unless you checkout
                                                    the repository to a custom
                                                    path
``requirements_path``   ``''``         ``false``    Relative path under
                                                    ``$repository_path`` to pip
                                                    requirements file
``sphinx_version``      ``''``         ``false``    Custom version of Sphinx
======================= ============== ============ =============================


Copy extra files to site
========================

Use Sphinx confval html_extra_path__.

__ https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-html_extra_path
