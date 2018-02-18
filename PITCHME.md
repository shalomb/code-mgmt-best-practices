# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">Infrastructure</span> as Code</span>

* Problems
* Best practices & Recommendations
* Examples

---

## Infrastructure As Code

Is it this?

![IaC - What Is It?](src/images/IaC-WhatIsIt2.jpg)

+++

## Infrastructure As Code

Or this?

![IaC - What Is It?](src/images/IaC-WhatIsIt.jpg)

+++

## Infrastructure As Code

![IaC - CiCd](src/images/cicd-pipeline.png)

+++

## Infrastructure As Code

![IaC - CiCd](src/images/cicd-pipeline.png)

- Delivery Target of multiple datacenters
- What are the current problems?
- How do we organize CI/CD to deliver this?

+++

## IaC - Generally accepted practices

- All deployments must be driven through code |
- All pipeline activities must be transparent |
- Production deployments must be effortless/consistent/automated |
- Delivery pipeline should support a "Fail Fast/Fail Early" strategy |
- All deployments must be repeatable, reliable |

---

## Current problems

- We are only beginning IaC journey - problems expected
- Problems noticed through observation
- Not everyone contributes to the problems
- Some problems are not obvious

---

## Problem - Dev on ControlVM

![IaC - Broken](src/images/iac-broken-1.png)

- Use of Control VMs to do dev/hotfixing |
- Has unfortunately become the de-facto way of working. |
- Not IaC!!! Opaque, Short-circuit to production |

+++

## Problem - Lost work

![IaC - Broken](src/images/iac-broken-1.png)

- All engineers logged on as the dtadmin user |
- Changes left on Control VMs, not committed back into git |
- Lost work. Has to be redone later |
- Not IaC!!! Repetition |

+++

## Problem - Shared workspace

![IaC - Broken](src/images/iac-broken-1.png)

- Multiple engineers sharing the same git workspace |
- One engineer wants to change branch, breaks everybody else |
- Potentially not noticed, changes baked into environment |
- Not IaC!!! Production state differs from code |

+++

## Problem - Undesired state

![IaC - Broken](src/images/iac-broken-1.png)

- Unhygienic environment on ControlVM |
- Undesired changes potentially pulled in by deploy scripts |
- Potentially not noticed, changes baked into environment |
- Not IaC!!! Production state differs from code |

+++

## Problem - Use of master branch

![IaC - Broken](src/images/iac-broken-2.png)

- Commiting straight into the master branch |
- Often as the dtadmin/root user |
- OK, but what about review/traceability? |
- Not IaC!!! Circumvents review/QA |

+++

## Problem - Insufficient testing

![IaC - Broken](src/images/iac-broken-2.png)

- Insufficient review |
- Insufficient testing |
- Not IaC!!! More hotfixing than usual QA-centric delivery |

+++

## Recommendations/Remediation

![IaC - CiCd](src/images/cicd-pipeline.png)

- Protect the production/master branch |
- Ensure production/master branch always reflects production state |

+++

![IaC - CiCd](src/images/cicd-pipeline.png)

- Use a staging/develop branch to collect MRs |
- Make this the default branch |
- Make use of a QA/Test env. for cont. integration/testing |
- Make use of a staging env. for release readiness |

+++

![IaC - CiCd](src/images/cicd-pipeline.png)

- If deployment is found to differ from code, hotfix right away |
- Ensure all Merge-Requests are reviewed |
- Recommend all commits are tested |

+++

![IaC - CiCd](src/images/cicd-pipeline.png)

- Publish guidance on use of dtadmin user - use sparingly |
- Changes to ControlVM should ideally be avoided |
- Changes to ControlVM should accompany change review |

---

## Slide Fragments
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |

<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Fragment-Slides) for details.</span>

+++

## Version Control - Branches

Git do's

- Prepare changes in topical branches |
- Commit early/often with small, logical commits |
- Reverts, cherry-picks, etc are simpler - as is testing/intuition.
- Commit often, perfect later - but publish once |
- Commit completed changes |
- Test before you commit - fail early |
- Test before you merge |
- Test after you merge?? |
- Commit binaries into source code (use Artifactory!! or Swift/Ceph!!) |

+++

Branches Do's

- Use private repositories for features-in-dev/experimental |
- Did you know about gitlab user repositories? |
- https://gitlab.tools.in.pan-net.eu/$username? |
- Mark your merge request as a WIP: until you're ready to merge |
- Write nice commit messages |
- Make use of smart commits |

+++

Branches Dont's

- Work on the master branch |
- Why? You should be able to context switch between dev and hotfixing. |
- Combine multiple unrelated changes into a single commit |
- Write commit message titles > 50 chars |
- Never change published history |
- Change published tags |
- Work within the GitLab UI |
- Pull before you push |

---

## Branching models

Branches are small, short-lived.
Feature selection/integration for release should happen at any time.
Where does integration happen? Into the production branch?????
Remember that feature may not be selected for release
were unrealistic/expensive, too risky, were experiments

Many models exist, which to choose for a team requires consideration.

+++

## Branching models ...

- Should support the release management processes
- Should support deployments at any time - disaster recovery
- Should support QA at every stage

+++

## Branching models ...

- Consideration: What are the important phases of your delivery lifecycle?
- Process-mature teams ensure that code goes through gates
- Feature/spring planning
- Selected-for-development
- Selected-for-integration
- Released-to-testing (integration->regression testing)
- Released-to-staging
- Released-to-production

+++

## Branching models ...

- Can you port groups of related change?
- Can a hotfix (quick and dirty) be formally backported into next release?
- Can you forward port a feature-in-development to a hotfix? Circumventing QA?
- Can you manage all of this one one branch?
- *Unlikely*

+++

## Branching models ...

- How do you do rollbacks?
- In code, you deploy from the previous last-known-good-tag.
- In infrastructure, you need to test the rollback.

+++

## Branching models ...

- How do you do hotfixes?

+++

## Branching models ...

- How should branches be named?
- *master*
- `develop`
- _feature/31-support-api-logging_
- **hotfix/42-fix-api-auth**
- ``release/v1.2.3``

+++

## Branching models ...

- What is the lifecycle of a branch?

---

## IaC best practices

- Treat all infrastructure as code |
- The code is the authority/source of truth |
- If the infrastructure tells a different story, a hotfix is needed |
- Does your codebase tell a story? It should, a healthy one |
- How do you improve the health of a codebase? |
- Testing, More Testing, code review, better practices |
- Allow rollbacks - things can go wrong |
- Allow for hotfixing and development to happen simulatenously |
- Use a workflow that supports your release strategy |
- is a single branch model the best for your team? |
- *Quite unlikely* |

---

## Release management

### Release readiness

- Test, test, test - has everything gone green. Really green??
- Generate release notes - high-level summary of changes
- Generate a CHANGELOG - what you would want your peers to see
- Tag your release commit appropriately
- Deploy from the tag
- Generate a release notification

---

### Versioning

Why? Makes communications easier.

- Alice: Hi Ted, We noticed a problem in production ... |
- Ted:   Ok, What version are you using? |
- Alice: Version <span style="font-size:0.6em; color:gray">13619bd</span> |
- Ted:   Yea, we had to hotfix that version, please move to <span style="font-size:0.6em; color:gray">13619bd</span> |
- Pointy-Haired Boss: What in ....'s name? |

+++

- Alice: Ok, but what changed in `db91631` ??
- Ted:   I have no time for this .. please `git log db91631` |
- Alice: It says, `overlay-orange#188 Fixing gobbledegook() in the foobar module` |
- Ted:   Yes? And? |
- Alice: I have no idea what that means .. |
- Alice: Do I need to re-test all my application? Or just the integration? |
- Ted:   How do I know? Does your application make use of `foobar`? |
- Alice: No. |
- Pointy-Haired Boss: %*!$£** |

+++

Alice: Hi Ted, We noticed a problem with `service foo v1.2.3` in production ...
Ted:   I see, but `v1.5.1` is current.

Pointy-Haired Boss: Alice, `v1.5` introduced changes to behaviour X ..
Alice: I see, OK, We shall try upgrading to `v1.5.1` ..

Pointy-Haired Boss: You will have to upgrade to `v1.4` first ..
Alice: I see. Thanks!! Wally, please upgrade in TestEnv1 and run a sanity test.

Wally: Done!! Two issues were raised, feature changes were prepared and ready to merge.
Alice: Ok, please merge them into `develop` and run the full regression test.

Publishing change/change-sets

- Use git tags on the `master` branch
- Deploy code from a tag.
- Why? Because `13619bd` means what. `v2.3.456` is better.
- Especially true if your code has downstream consumers.
- Recommendation: Make use of [Semantic Versioning 2.0.0](https://semver.org/)

+++

<span style="font-size:0.6em; color:gray">Authors</span> |

- Use tags on your `master` branch to signal downstream change.
- Tags must be of the form `<major-version>.<minor-version>.<patch-version>`.
- Encourage your users/downstream teams to use your tagged.
- Know your rollback procedure i.e. `deploy-script --version v1.2.2`
- Know your hotfix procedure i.e. `git checkout tags/v1.2.3 -b hotfix/broken-api`

+++

<span style="font-size:0.6em; color:gray">User</span>

+++

Example manifest for ansible-galaxy

@[1](Specify your dependency)
@[3](Specify the version of the dependency)
@[5-6](Dependency may come from another source)
@[9-14](Python for..else statement)

---
