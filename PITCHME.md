## Code Management

##### <span style="font-family:Helvetica Neue; font-weight:bold">A <span style="color:#e49436">Git</span>Pitch Feature Tour</span>

* Best practices
* Recommendations
* Examples

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
- Can you forward port a feature-in-development to a hotfix?
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
- master
- develop
- feature/31-support-api-logging
- hotfix/42-fix-api-auth
- release/v1.2.3

+++

## Branching models ...

- What is the lifecycle of a branch?

---

## IaaC best practices

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

```yaml
- src: git@gitlab:my-project/module-a
  scm: git
  version: v1.2.3

- src: git+http://artifactory/ansible-roles/role-b/
  version: v3.2.1

- src: http://artifactory/ansible-roles/role-b-v1.2.3.zip

- src: git@gitlab:my-project/module-d
  scm: git
  version: v1.2.3

- src: git@gitlab:my-project/module-e
  scm: git
  version: v1.2.3

- src: git@gitlab:my-project/module-f
  scm: git
  version: v1.2.3
```

@[1](Specify your dependency)
@[3](Specify the version of the dependency)
@[5-6](Dependency may come from another source)
@[9-14](Python for..else statement)

---

#### Use GitHub Flavored Markdown
#### For Slide Content Creation

<br>

The same tool you use to create project **READMEs** and **Wikis** for your Git repos.

---

## Code Presenting
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Code-Presenting) for details.</span>

+++

#### Present Source Directly From Your Repo

<br>

Step through source code directly within your presentations,
no more switching back and forth between your slideshow and your IDE!

+++

#### Or Present Static Code Blocks

<br>

Enjoy code syntax highlighting for dozens of languages powered by [highlight.js](tlhttps://highlightjs.org).

+++

```python
from time import localtime

activities = {8: 'Sleeping', 9: 'Commuting', 17: 'Working',
              18: 'Commuting', 20: 'Eating', 22: 'Resting' }

time_now = localtime()
hour = time_now.tm_hour

for activity_time in sorted(activities.keys()):
    if hour < activity_time:
        print activities[activity_time]
        break
else:
    print 'Unknown, AFK or sleeping!'
```

@[1](Python from..import statement)
@[3-4](Python dictionary initialization block)
@[6-7](Python working with time)
@[9-14](Python for..else statement)

---

## GIST Slides
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/GIST-Slides) for details.</span>

+++

#### GitHub GIST
#### Building Blocks For Any Presentation

<br>

Enjoy 100% reusable code snippets, excellent syntax highlighting, code indentation and styling.

+++?gist=8da53731fd54bab9d5c6

+++?gist=28ee3d19ddef9d51b15adbdfe9ed48da

---

## Image Slides
## [ Inline ]
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Image-Slides) for details.</span>

+++

#### Make A Visual Statement

<br>

Use inline images to lend a *visual punch* to your slideshow presentations.


+++

<span style="color:gray; font-size:0.7em">Inline Image at <b>Absolute URL</b></span>

![Image-Absolute](https://d1z75bzl1vljy2.cloudfront.net/kitchen-sink/octocat-privateinvestocat.jpg)


<span style="color:gray; font-size: 0.5em;">the <b>Private Investocat</b> by [jeejkang](https://github.com/jeejkang)</span>


+++

<span style="color:gray; font-size:0.7em">Inline Image at GitHub Repo <b>Relative URL</b></span>

![Image-Absolute](assets/octocat-de-los-muertos.jpg)

<span style="color:gray; font-size:0.5em">the <b>Octocat-De-Los-Muertos</b> by [cameronmcefee](https://github.com/cameronmcefee)</span>


+++

<span style="color:gray; font-size:0.7em"><b>Animated GIFs</b> Work Too!</span>

![Image-Relative](https://d1z75bzl1vljy2.cloudfront.net/kitchen-sink/octocat-daftpunkocat.gif)

<span style="color:gray; font-size:0.5em">the <b>Daftpunktocat-Guy</b> by [jeejkang](https://github.com/jeejkang)</span>

---

## Image Slides
## [ Background ]
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Image-Slides#background) for details.</span>

+++

#### Make A Bold Visual Statement

<br>

Use high-resolution background images for maximum impact.

+++?image=https://d1z75bzl1vljy2.cloudfront.net/kitchen-sink/victory.jpg

+++?image=https://d1z75bzl1vljy2.cloudfront.net/kitchen-sink/127.jpg


---

## Video Slides
## [ Inline ]
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Video-Slides) for details.</span>

+++

#### Bring Your Presentations Alive

<br>

Embed *YouTube*, *Vimeo*, *MP4* and *WebM* inline on any slide.

+++

![YouTube Video](https://www.youtube.com/embed/dNJdJIwCF_Y)

+++

![Vimeo Video](https://player.vimeo.com/video/125471012)

+++

![MP4 Video](http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4)


---

## Video Slides
## [ Background ]
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Video-Slides#background) for details.</span>

+++

#### Maximize The Viewer Experience

<br>

Go fullscreen with *MP4* and *WebM* videos.

+++?video=http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4

---

## Math Notation Slides
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Math-Notation-Slides) for details.</span>

+++

#### Beautiful Math Rendered Beautifully

<br>

Use *TeX*, *LaTeX* and *MathML* markup powered by [MathJax](https://www.mathjax.org).

+++

`$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$`

+++

`\begin{align}
\dot{x} & = \sigma(y-x) \\
\dot{y} & = \rho x - y - xz \\
\dot{z} & = -\beta z + xy
\end{align}`

+++

##### The Cauchy-Schwarz Inequality

`\[
\left( \sum_{k=1}^n a_k b_k \right)^{\!\!2} \leq
 \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
\]`

+++

##### The probability of getting \(k\) heads when flipping \(n\) coins is:

`\[P(E) = {n \choose k} p^k (1-p)^{ n-k} \]`

+++

##### In-line Mathematics

This expression `\(\sqrt{3x-1}+(1+x)^2\)` is an example of an inline equation.

---

## Chart Slides
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Chart-Slides) for details.</span>

+++

#### Chart Data Rendered Beautifully

<br>

Use *Bar*, *Line*, *Area*, and *Scatter* charts among many other chart types directly within your markdown, all powered by [Chart.js](http://www.chartjs.org).

+++

<canvas data-chart="line">
<!--
{
 "data": {
  "labels": ["January"," February"," March"," April"," May"," June"," July"],
  "datasets": [
   {
    "data":[65,59,80,81,56,55,40],
    "label":"My first dataset","backgroundColor":"rgba(20,220,220,.8)"
   },
   {
    "data":[28,48,40,19,86,27,90],
    "label":"My second dataset","backgroundColor":"rgba(220,120,120,.8)"
   }
  ]
 },
 "options": { "responsive": "true" }
}
-->
</canvas>

+++

<canvas class="stretch" data-chart="horizontalBar">
<!--
{
 "data" : {
  "labels" : ["Grapefruit", "Orange", "Kiwi",
    "Blackberry", "Banana",
    "Blueberry"],
  "datasets" : [{
    "data": [48, 26, 59, 39, 21, 74],
    "backgroundColor": "#e49436",
    "borderColor": "#e49436"
  }]
  },
  "options": {
    "title": {
      "display": true,
      "text": "The most delicious fruit?",
      "fontColor": "gray",
      "fontSize": 20
    },
    "legend": {
      "display": false
    },
    "scales": {
      "xAxes": [{
        "ticks": {
            "beginAtZero": true,
            "max": 80,
            "stepSize": 10,
            "fontColor": "gray"
        },
        "scaleLabel": {
          "display": true,
          "labelString": "Respondents",
          "fontColor": "gray"
        }
      }],
      "yAxes": [{
        "ticks": {
            "fontColor": "gray"
        }
      }]
    }
  }
}
-->
</canvas>

---

## Slide Fragments
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |

<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Fragment-Slides) for details.</span>

+++

#### Reveal Slide Concepts Piecemeal

<br>

Step through slide content in sequence to slowly reveal the bigger picture.

+++

- Java
- Groovy |
- Kotlin |
- Scala  |
- The JVM rocks! |

+++

<table>
  <tr>
    <th>Firstname</th>
    <th>Lastname</th>
    <th>Age</th>
  </tr>
  <tr>
    <td>Jill</td>
    <td>Smith</td>
    <td>25</td>
  </tr>
  <tr class="fragment">
    <td>Eve</td>
    <td>Jackson</td>
    <td>94</td>
  </tr>
  <tr class="fragment">
    <td>John</td>
    <td>Doe</td>
    <td>43</td>
  </tr>
</table>

---
## <span style="text-transform: none">PITCHME.yaml</span> Settings
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Settings) for details.</span>

+++

#### Stamp Your Own Look and Feel

<br>

Set a default theme, custom logo, custom css, background image, and preferred code syntax highlighting style.

+++

#### Customize Slideshow Behavior

<br>

Enable auto-slide with custom slide intervals, presentation looping, and RTL flow.


---
## Slideshow Keyboard Controls
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Fullscreen-Mode) for details.</span>

+++

#### Try Out These Great Features Now!

<br>

| Mode | On Key | Off Key |
| ---- | :------: | :--------: |
| Fullscreen | F |  Esc |
| Overview | O |  O |
| Blackout | B |  B |
| Help | ? |  Esc |


---

## GitPitch Social
<span style="font-size:0.6em; color:gray">Press Down key for examples.</span> |
<span style="font-size:0.6em; color:gray">See [GitPitch Wiki](https://github.com/gitpitch/gitpitch/wiki/Slideshow-GitHub-Badge) for details.</span>

+++

#### Slideshows Designed For Sharing

<br>

- View any slideshow at its public URL
- [Promote](https://github.com/gitpitch/gitpitch/wiki/Slideshow-GitHub-Badge) any slideshow using a GitHub badge
- [Embed](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Embedding) any slideshow within a blog or website
- [Share](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Sharing) any slideshow on Twitter, LinkedIn, etc
- [Print](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Printing) any slideshow as a PDF document
- [Download and present](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Offline) any slideshow offline

---

## GO FOR IT.
## JUST ADD <span style="color:#e49436; text-transform: none">PITCHME.md</span> ;)
<span style="font-size:0.6em; color:gray">Available bottom-left of screen.</span> |
<span style="font-size:0.6em; color:gray">Start switching themes right now!</span>


