# Purpose
Setting up Python is easy.

Setting up Python so multiple developers can work on a project effectively, deploy it, and not spend most of their time 
dealing with path issues and the like is not.

This doc and example project can show you a couple of ways I've found with experience to get python up and running
on a system, and make it so you can focus on actual development!

# Getting started

- Use either Linux or Mac.  Python treats most files like strings, and so the need of Windows to deal with backslashes for files will always cause problems.
  - But I really want to use Windows and . . . . again, don't do it.  Setup a Xubuntu VM, or you can **maybe** try the Windows linux vm.
  - But Paths is platform aware and . . . . yeah, but lots of the libraries aren't, so then the problem reappears at the worst possible place.  
- Use [asdf](https://asdf-vm.com/) to manage lots of versions of Python
  - almost all real world applications wind up needing a specific version of Python, or you have a developer that won't upgrade, etc. ASDF makes it easy to manage them, and can do much more than pyenv without mangling your Virtual Environments
  - once you have asdf, install your Python prereqs (this varies with OS)
    - Linux ```sudo apt-get install -y make build-essential libssl-dev zlib1g-dev \
       libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
       libncurses5-dev libncursesw5-dev xz-utils tk-dev```
    - Mac - generally install Python3 with homebrew, then you'll have the prereqs (ASDF will take over python)
- Use Virtual Environments.  Always.  Don't ever do anything with your base install of Python, that is there
  - I mean anything.  If I install a tool that uses Python, I create a virtual environment first by making a directory, then running `python -m venv .venv` and `source .venv/bin/activate`.  That way my python environments are never polluted
- Use Poetry - it makes virtual environments and repeatable, as well as managing dependencies
  - pip and requirements.txt can work, but that solution is non-deterministic.  Poetry stops you needing to manage that.
  - You can also easily install Poetry with asdf
    - `asdf plugin add poetry`
    - `asdf install poetry latest`
    - `asdf global poetry latest`
  - configure Poetry to make virtual environments in project.  This can help if you want to easily blow them away, look at imports, etc
    - `poetry config virtualenvs.in-project true
`

You should now have Poetry and Python ready to go!


# Project Structure
See the attached project for a usual setup in a monorepo.  

I know, I know - "what the hell is library/library" about?
 
So Poetry does support using a `src` folder with the `--src` option on `poetry new` ([here](https://python-poetry.org/docs/cli/)).  But it isn't the default on purpose.

This has been a big fight for 5+ years (see [here](https://github.com/pypa/packaging.python.org/issues/320)).  The main reason
it avoids src is that the workaround to avoid `import src.x` and instead use `import x` breaks with packaging, especially if multiple modules
are needed.

Tests should go into the `tests` folder.  Any subfolders of `project/project` can be reflected so the structures are identical.

# Repositories
If you have more than one project that need to use the same code, you can create a library.  

To host your library, you can use either a public or private repository.  For private repositories, 
[Artifact Registry](https://cloud.google.com/artifact-registry/docs/python/store-python) can be used to store python libraries.

You will build and release them using Poetry, specially the `poetry publish` command.  See [private repository setup in Poetry](https://python-poetry.org/docs/repositories/).

If they don't actually need to be changed on different cadences, then you don't need multiple projects.  If you need for organization, you can
have more than one module per poetry project.  So you'll wind up with a structure like

- myproject
   - email_stuff
   - webserver
   - pyproject.toml
   - Dockerfile - for the webserver, which can use the code in email_stuff

etc.  This can be much more lightweight than publishing a 

But Python code should **never** reach outside of its project (meaning outside the folder containing project.toml).  Python
will let you do this, but it's always a bad idea.

