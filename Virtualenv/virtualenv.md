#Vitualenv

keep your project in independent develop environment

##virtualenv  
* Install:	
	`$ sudo apt-get install python-virtualenv`

* Create virtualenv:
	`$ virtualenv <env_name>`

* Run env:
	`$ source ./bin/active`

__Note : The most painful approach:(__

##[virtualwrapper](https://bitbucket.org/dhellmann/virtualenvwrapper)
* Install:
	`$ sudo pip install virtualenvwrapper`
	`$ export WORKON_HOME=$HOME/.virtualenvs`
	`$ source /usr/local/bin/virtualenvwrapper.sh`
	`$ source ~/.bashrc`

* Create virtualenv
	`$ makevirtualenv <env_name>`

*  Run env
	`$ workon <env_name>`

__Note : The most frequent used approach:)__


## References
* [virtualenv doc](https://virtualenv.readthedocs.org/en/latest/)
* [virtualenvwrapper doc](http://virtualenvwrapper.readthedocs.org/en/latest/index.html)