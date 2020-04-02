SrcOpsMtrcs
------------

This repository contains functions to store knowledge for the bot,
to use the knowledge stored by the bot to evaluate some statistics.

Pre-Usage
=========

.. code-block:: console

    pipenv install --dev

Usage - Create Bot Knowledge
============================

1. You can extract knowledge from a repository using the following command:

.. code-block:: console

    GITHUB_ACCESS_TOKEN=<github_acess_token> PYTHONPATH=. pipenv run srcopsmetrics/cli.py --repository <repo_name> -c

2. You can extract knowledge from a organization using the following command:

.. code-block:: console

    GITHUB_ACCESS_TOKEN=<github_acess_token> PYTHONPATH=. pipenv run srcopsmetrics/cli.py --organization <org_name> -c

Usage - Storing Knowledge
====================================

By default the cli will try to store the bot knowledge on Ceph.
In order to store on Ceph you need to provide the following env variables:

- `S3_ENDPOINT_URL` Ceph Host name where knowledge is stored.
- `CEPH_BUCKET` Ceph Bucket name where knowledge is stored.
- `CEPH_BUCKET_PREFIX` Ceph Prefix where knowledge is stored.
- `CEPH_KEY_ID` Ceph Key ID
- `CEPH_SECRET_KEY` Ceph Secret Key

If you want to test locally you have also the option to store locally without providing any parameter adding `-l` flag:

.. code-block:: console

    GITHUB_ACCESS_TOKEN=<github_acess_token> PYTHONPATH=. pipenv run srcopsmetrics/cli.py --repository <repo_name> -c -l

Usage - Visualize Project Statistics
====================================

.. code-block:: console

    PYTHONPATH=. pipenv run srcopsmetrics/cli.py --repository <repo_name> -v

.. code-block:: console

    PYTHONPATH=. pipenv run srcopsmetrics/cli.py --organization <org_name> -v

Examples
=========
For each repository is possible to obtain the following plots:

`MTTFR-in-time.png` --> Mean time to First Review (MTTFR) variation after each PR approved in time.

`MTTR-in-time.png` --> Mean time to Review (MTTR) variation after each PR approved in time.

`TTCI-in-time.png` --> Time to Close an Issue (TTCI) variation after each PR approved in time.

`TTFR-in-time.png` --> Time to First Review (TTFR) variation after each PR approved in time.

`TTR-in-time.png` --> Time to Review (TTR) variation after each PR approved in time.

`TTR-per-PR-length.png` --> Time to Review (TTR) variation after each PR length.

`TTR-per-PR.png` --> Time to Review (TTR) variation after each PR.

`TTFR-per-PR-length.png` --> Time to First Review (TTFR) variation after each PR length.

`TTFR-per-PR.png` --> Time to First Review (TTFR) variation after each PR.

Usage - Reviewer Reccomender
============================

.. code-block:: console

    PYTHONPATH=. pipenv run srcopsmetrics/cli.py --project <project_name> -r True

If there are bots in the list of contributors of your project you can add them to the list
at the beginning of the file. In this way you can receive the percentage of the work
done by humans vs bots.

.. code-block:: console

    BOTS_NAMES = [
        "sesheta",
        "dependencies[bot]",
        "dependabot[bot]",
        ]

`number_reviewer` flag is set to 2

Final Score for Reviewers assignment
=====================================

The final score for the selection of the reviewers, it is based on the following
contributions. (Number of reviewers is by default 2, but it can be changed)

1. Number of PR reviewed respect to total number of PR reviewed by the team.

2. Mean time to review a PR by reviewer respect to team repostiory MTTR.

3. Mean length of PR respect to minimum value of PR length for a specific label.

4. Number of commits respect to the total number of commits in the repository.

5. Time since last review compared to time from the first review of the project respect to the present time.
(Time dependent contribution)

Each of the contribution as a weight factor k. If all weight factors are set to 1,
all contributions to the final score have the same weight.

Example results
===============

.. code-block:: console

               Repository  PullRequest n.  Commits n.  PullRequestRev n.           MTTFR     MTTR

thoth-station/performance              33          38                 20  0:17:30.500000  0:46:28
INFO:reviewer_recommender:-------------------------------------------------------------------------------

Contrib  PR n.      PR %  PRRev n.  PRRev % MPRLen  Rev n.  MRL    MTTFR     MTTR                     TLR  Comm n.  Comm %    Bot
fridex     17  0.515152        13     0.65      S      21  3.0  0:02:44  0:31:10 40 days 00:08:36.857380       19     0.5  False
pacospace  16  0.484848         7     0.35      M       9  1.0  1:01:46  1:01:46 40 days 05:00:39.857380       19     0.5  False

Contrib        C1        C2       C3   C4  C5     Score
pacospace  0.484848  0.752294  1.00000  0.5   1  0.337028
fridex     0.515152  1.490909  0.22449  0.5   1  0.159314

INFO:reviewer_recommender:Number of reviewers requested: 2
INFO:reviewer_recommender:Reviewers: ['pacospace' 'fridex']
