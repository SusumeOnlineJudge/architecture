# susume online judge

An online judge is a website that provides competitive programming task
descriptions, accepts solutions in the form of source code, verifies the
correctness of those solutions through automated testing, and presents the
results to the user, or in case a contest is being run, to the jury of the
contest. [Wikipedia article on competitive
programming](https://en.wikipedia.org/wiki/Competitive_programming)

susume is online judge software under development with the goal of surpassing
[SIO2/OIOIOI](https://github.com/sio2project/oioioi), which is the online judge
software of [Polish Olympiad in Informatics](https://oi.edu.pl).

## Goals

### The user must never see a 502 Bad Gateway error on contest-related and submission-critical views during a contest

OIOIOI was written in Python programming language using Django web framework.
Python is an interpreted language. Although there exists a JIT runtime called
PyPy, it is not always super compatible with libraries created for the
interpreted runtime (e.g. different handling of C extensions), we have not
been desperate enough to use it on production yet.

Django brands itself as the "the Web framework for perfectionists with
deadlines" and that's a pretty good summary of what it is: a great piece of
digital machinery which strives to help developers write correct and good enough
web applications quickly. It handles the database pretty well (it can even
generate migrations between versions of the database schema for you), it helps
you handle user input safely, it provides you with an authentication system for
you – there's a lot to like about it.

However, with mountains of Python code Django is made of (and OIOIOI is a high
mountain already as well), it is known to not have a super great performance.
Django is good enough for a lot of cases — for simpler applications that don't
get a lot of traffic or are not time-critical — but at the Olympiad, if your
submission comes a little too late due to servers being overwhelmed, there's a
risk the system will reject it because you missed a deadline.

This risk has thus far been mitigated through various means: for remote stages,
online submissions end at noon but you can still submit via post until midnight;
for onsite stages, you can use a commandline submission tool which is permitted
to submit past the deadline once the server load is nominal again. However,
recently Polish Junior Olympiad in Informatics has run a timed quiz on OIOIOI as
part of its first stage and the servers were overwhelmed by contestants. The
server configuration was tuned to account for this, but the trauma remains.

Attempts at measuring performance showed that an alarming amount of time in
OIOIOI is spent rendering templates. Django project itself confirms in its
documentation that its templating engine is not the fastest one in the market,
but redoing the whole frontend with another engine sounds pretty difficult.

Therefore it is critical to this project that there are no performance issues
that could impend a contestant's participation in the contest. If Python's flaws
cannot be overcome, it should be replaced with one that has a stable JIT runtime
or one that runs on bare metal (which is how we refer to Linux VMs in 2020), and
a web framework should be chosen that is highly performant.


### All submission attempts must be preserved

In the previous section I mentioned mitigations for servers being overwhelmed by
traffic. I will now expand on the junior timed quiz incident I mentioned:

One part of the Junior Olympiad is a quiz, where some questions are algorithmic
and some are strictly related to the programming language the participant chose.
Starting this year, the quiz can no longer be taken at any chosen point in a few
months' timespan, but instead has to be completed in a strict 90 minutes period.

Turns out that this poses a major problem when matched with OIOIOI's inability
to handle high server load (especially when forced to render quizzes a lot).
Another funny thing is that multiple choice answers require at least one answer
to be chosen, and if nothing is chosen, the form validation fails. Children do
not know it (and perhaps, shouldn't need to know it), submit at last possible
moment, only to discover that submission failed.

For now, our scientists have come up with a scheme that allows us to
continuously log the last known partially good answer with some AJAX magic and a
separately running Postgres-backed HTTP-accessed key-value store. But some of
them argue that we should consider integrating this kind of mechanism into the
whole submission process: to tentatively accept partially invalid submissions
for later human review.


### Contest jury shouldn't have too much access to personal data

TODO: explain how nice it would be to be on good terms with GDPR


## Removed restrictions

OIOIOI has a bunch of nice properties, but we might not need them here

### Jury doesn't have to be able to create the contest themselves

TODO: explain what about OIOIOI makes them too afraid to do that


### Code that was custom written shouldn't need to be maintained for eternity

TODO: explain why maintaining OIOIOI is a pain
