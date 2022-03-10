![GitHub Classroom Workflow](../../workflows/GitHub%20Classroom%20Workflow/badge.svg?branch=main)
<img alt="points bar" align="right" height="36" src="../../blob/badges/.github/badges/points-bar.svg" />

# COMP0061 -- Privacy Enhancing Technologies -- Lab 05

This lab takes you step-by-step through building a simple credential based pseudonym system.

### Structure of Labs
The structure of all the labs will be similar: two Python files will be provided. 

- The first is named `lab0x.py` and contains the structure of the code you need to complete. 
- The second is named `lab0X_test.py` and contains unit tests (written for the Pytest library) that you may execute to 
partially check your answers. 

Note that the tests passing is a necessary but not sufficient condition to fulfill each task.
There are programs that would make the tests pass that would still be invalid (or blatantly insecure) implementations.

The only dependency your Python code should have, besides Pytest and the standard library, is the Petlib library, 
which was specifically developed for this course (and also for our own use!). 

The petlib documentation is [available on-line here](http://petlib.readthedocs.org/en/latest/index.html).

### Checking out code

Check out the code by using your preferred git client (e.g., git command line client, Github Desktop, Sourcetree).
**Alternatively**, if you are using Visual Studio Code, click the `Open in Visual Studio Code` button above to automatically check
out and open the repository. Visual Studio Code also allows the use of 
[development containers](https://code.visualstudio.com/docs/remote/containers).

### Setup
The intended environment for this lab is the Linux operating system with Python 3 installed.

#### Local virtual environment

We provide a `setup.sh` file that creates a local virtual environment, installs the dependencies needed for the lab,
and activates the virtual environment. To run the setup file, type `source setup.sh` into the terminal. The virtual
environment is needed to run the unit tests locally. 

#### Visual Studio Code development containers

As an alternive to a local virtual environment, we provide the setup files for VSCode 
[development containers](https://code.visualstudio.com/docs/remote/containers)
which use [Docker](https://docs.docker.com/get-docker/) to create a separate development environment for each 
repository and install the required libraries. You don't need to know how to use Docker to use development containers.

#### Github tests

The tests are the same as the ones that run as part of the Github Classroom automated marking system, 
so you can also run the tests by simply committing and pushing your changes to Github, without the need for a local 
setup or even having Python 3 installed.

### Working with unit tests
Unit tests are run from the command line by executing the command:

```sh
$ pytest -v
```

Note the `-v` flag toggles a more verbose output.
If you wish to inspect the output of the full tests run you may pipe this command to the `less` utility 
(execute `man less` for a full manual of the less utility):

```sh
$ pytest -v | less
```

You can also run a selection of tests associated with each task by adding the Pytest marker for each task to the Pytest
command:

```sh
$ pytest -v -m task1
```
The markers are defined in the test file and listed in `pytest.ini`.

You may also select tests to run based on their name using the `-k` flag.
Have a look at the test file to find out the function names of each test.
For example the following command executes the very first test of Lab 1:

```sh
$ pytest -v -k test_user_encryption
```

The full documentation of pytest is [available here](http://pytest.org/latest/).


### What you will have to submit
The deadline for all labs is at the end of term but labs will be progressively released throughout the term, as new
concepts are introduced. 
We encourage you to attempt labs as soon as they are made available and to use the dedicated lab time to bring up any
queries with the TAs.

Labs will be checked using Github Classroom, and the tests will be run each
time you push any changes to the `main` branch of your Github repository.
The latest score from automarking should be shown in the Readme file.
To see the test runs, look at the Actions tab in your Github repository. 

Make sure the submitted `lab05.py` file at least satisfies the tests, without the need for any external dependency 
except the Python standard libraries and the Petlib library. 
Only submissions prior to the Github Classroom deadline will be marked, so make sure you push your code in time.


To re-iterate, the tests passing is a necessary but not sufficient condition to fulfill each task.
All submissions will be checked by TAs for correctness and your final marks are based on their assessment of your work.  
For full marks, make sure you have fully filled in any sections marked with `TODO` comments, including answering any
questions in the comments of the `lab05.py`.

## Lab 05 -- Selective Disclosure (Anonymous) Credentials

### Big picture  

- A user registers a secret value with an identity provider that issues 
them with a credential. The secret value is encrypted using the Benaloh encryption scheme
we have studied before, and a the ciphertext proven correct. The issuer then obliviously issues a credential with the secret value as the only attribute. 
The user may verify the issuing was indeed correct. Finally, the user may use this credential to prove its possession. More interestingly the user may derive a stable, service specific pseudonym bound to their secret value v, but that is unlinkable across services.

- *What is the point of this system?* You may use the techniques in this exercise to implement a simple identity provider. Other services use the issued credentials to authenticate users to service specific pseudonyms. These pseudonyms are not linkable across services. This way a user can establish long term relations with each service without fearing being profiled across services. For example a UCL student issued with a credential may create a different pseudonym when using different university libraries; they all know, and can authenticate, the student under a different stable ID; however, even if they collate their records they cannot not get a full picture of all the books the student read.
- This exercise makes extensive use of the MAC_GGM construction of credentials as described in ["Algebraic MACs and Keyed-Verification Anonymous Credentials"](https://eprint.iacr.org/2013/516.pdf) pages 8-9. Have it at hand while you do this lab.

## Task 1 -- User encrypts secret attribute v \[1 point\]

- In this task the user derives a fresh random attribute v, and encrypts it using a homomorphic encryption scheme under their own public key. The ciphertext will be used by the issuer to issue a credential on this attribute blindly, i.e. without ever learning the attribute.

- The encryption is performed for you, but you have to provide a Zero-knowledge proof of knowledge on a number of statements: You need to prove knowledge of the encryption randomess k, the secret v and the private key for he used public key.

- Note the verification of the proof above is provided: study it and ensure your proof may be verified correctly. The comment in task 1 also describes the statements to be proved.

- The method used for issuing a credential on an encrypted (hidden) attribute is different from the one we covered in the lectures, where we looked at public / disclosed attributes. The technique used here is described in the last paragraph (page 8) of the paper on algebraic MACs.

## Task 2 -- Issuing and issuing verification \[1 point\]

- In this task an issuer uses the provided ciphertext of the attribute to MAC it, thus providing a credential with 1 attribute. This is done using the ciphertext of v provided therefore keeping v secret from the issuer.

- The steps necessary to issue a credential on a single blinded attribute are listed in the code file. They map to the hidden attribute "Issuance" protocol described in the last paragraph of page 8 in the aMAC paper.

- Not only the issuer provides a MAC (u, u_prime) (where u_prime is encrypted), but also a proof of correct issuing. The statement that needs to be proved is provided in the code, and you may use it to further understand what computations are necessary as part of the issuance protocol.

- The credential issuance verification and decryption procedures are provided. Study them to ensure they can verify the issuance and proof you construct.

- Note that is a real-world application the issuer would first authenticate the user, before issuing the credential.

## Task 3 -- Showing and verifying a showing of a credential. \[1.5 point\]

- In this task a user issued with a credential (u, u_prime) with a single attribute v, proves that they hold such a credential on a secret value v without revealing v, and in a manner that is unlinkable to previous showings or the issuing.

- The steps necessary to prove possession are commented in the code. It is important to blind the credential, and then compute all necessary values described in page 9 of the algebraic MACs paper under the "Show" protocol.

- A ZK proof of correct construction has to be computed. The exact statement to be proven is provided, and follows closely a 1 attribute Showing protocol from the paper.

- For this task you need to implement the verification of the credential showing. This is the protocol "ShowVerify" page 9 of the reference paper. Note that you have to re-compute V on the verifier side, without transmitting it.

- In a real setting, the output of the Showing protocol would be reflected by a service back to the identity provider, that would verify it and return whether it is a valid proof. This on-line check makes it imperative for the issuing and showing protocols to be unlinkable, so that the issuer may not track which services are used by users.

## Task 4 -- Bind to a pseudonym \[1.5 point\]

- In the previous task the user Shows the credential therefore proving they are some user of the system (eg. some student at UCL). In this task the secret value v is used to derive a service specific pseudonym that is stable in the long term. The pseudonym is H(service name)^v.

- Your task is to modify the protocols of task 3 (above) to not only prove possession of a credential when showing it, but also proving that a credential's secret attribute v maps to the pseudonym claimed.

- Feel free to re-use code from Task 3 -- the modification to also support pseudonyms is small. Note that the paper presents proving arbitrary predicates on attributes when Showing as a predicate phi(m_1, ..., m_n). Showing that the attribute v leads to a pseudonym H(.)^v is a simple case of such a predicate.

## Task Q1 -- e-cash question \[1 point\]

- Answer the question in the space provided.
