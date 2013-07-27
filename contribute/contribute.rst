Contributing
============
One of the great things about open source projects is that anyone can contribute code. 
AlphaLemon CMS has thrived thanks to the contributions of numerous people and we want 
to encourage everyone to submit their patches freely. To help you in that process, 
there are several things that you should keep in mind.


Use Pull Requests
-----------------
If you want to submit code, please use a GitHub pull request. This is the fastest way 
for us to evaluate your code and to merge it into the code base. Please don't file 
an issue with snippets of code. Doing so means that we need to manually merge the changes 
in and update any appropriate tests. That decreases the likelihood that your code is going 
to get included in a timely manner. Please use pull requests.


Report a bug or ask for new features
------------------------------------
When you find a bug or you want to propose the implementation of a new feature, you
should open an `issue at github`_.


Contribute to AlphaLemon CMS Core
---------------------------------
When you want to contribute to AlphaLemon CMS Core by fixing issues or add new features,
you should fork the repository, make your changes and use pull request to let us review
your patch.


Unit Tests
^^^^^^^^^^
Regardless of the changes you are making, please make sure that your pull request 
includes appropriate tests. 

If you're submitting a change to AlphaLemon CMS core, update or add to the units 
tests in the Tests directory. 

If you are submitting a new App-Block, be sure to have properly added unit test to cover
the code and functional tests when your bundle uses internal controllers.

More about tests is explained in `this document`_.


Write new tests
---------------
While the most part of code is covered by automated tests, these may always be improved
by new tests you may write.


Submit your Theme or Block bundles
----------------------------------
If you want to share your awesome Theme or Block bundles with the community, you need to maintain
it on a repository at github.

To have your bundle distributed on AlphaLemon CMS website, open a pull request at 
`AlphaLemon CMS repository`_ and post the link to your repository, in the pull request body.

Your Theme or Block bundle will be evaluated and then it will be published.


Contribute with the documentation
---------------------------------
AlphaLemon CMS has its official documentation on a dedicated `repository at github`_. If 
you wanto to contribute fixing typos on the current documentation or add new entries, just
fork this repository and send us your pull requests.


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`issue at github`: https://github.com/alphalemon
.. _`this document`: how-to-run-the-alphalemon-cms-test-suite
.. _`AlphaLemon CMS repository`: https://github.com/alphalemon/AlphaLemonCmsBundle/pulls
.. _`repository at github`: https://github.com/alphalemon/alphalemon-docs