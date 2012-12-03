# Dealing with many Pythons using Pythonbrew

We recently had to work with Python 3 while not breaking other versions we had on our system. While many strategies are available, we discovered [Pythonbrew](https://github.com/utahta/pythonbrew) which achieves just that efficiently. Think of Pythonbrew as a [RVM](https://github.com/wayneeseguin/rvm) for Python. 

Once Pythonbrew is installed on your system, you can install, use and switch Python versions as easilly as:

	$ pythonbrew install 2.7.3 3.2
	$ pythonbrew switch 2.7.3
	$ python --version
	$ 2.7.3
	$ pythonbrew switch 3.2
	$ python --version
	$ 3.2

Creating a virtual env for a given version is just as easy:

	$ pythonbrew switch 3.2
	$ pythonbrew venv create my32env
	$ pythonbrew venv use my32env
	# Using `my32env` environment (found in /Users/niko/.pythonbrew/venvs/Python-3.2)
	# To leave an environment, simply run `deactivate`
	$ type -a python
	python is /Users/niko/.pythonbrew/venvs/Python-3.2/my32env/bin/python
	python is /Users/niko/.pythonbrew/pythons/Python-3.2/bin/python
	python is /usr/bin/python

[Pythonbrew](https://github.com/utahta/pythonbrew) is a great project, use it.
