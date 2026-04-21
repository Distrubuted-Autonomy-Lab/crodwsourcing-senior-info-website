# Accessibility Design and Research

**Carebot Crowdsourcing Module, Sprint 3**
**Cole Segura, CS 495 Capstone, Spring 2026**

---

## Purpose

The Sprint 2 accessibility guide set the rules: minimum touch target sizes, minimum text sizes, contrast ratios, form labeling, and no color-alone status indicators. This document explains the research behind those rules and the users they serve. It is meant to be read alongside `ACCESSIBILITY-GUIDE.md`, not in place of it.

Dr. Anderson's framing for this project was simple. If a grandmother who does not know much about technology can use the interface without frustration, we have done our job. That sentence is not casual. It is a design constraint that touches every decision on the page, and it is supported by a surprising amount of real research.

---

## Who we are actually designing for

The people submitting caregiver resources to Carebot are not the same people who normally use modern web apps. The actual user base skews older, less technically fluent, and more likely to be dealing with stress, fatigue, or grief than the average software user.

According to the Alzheimer's Association 2024 report, more than 11 million Americans provide unpaid care for people living with Alzheimer's or another dementia. The majority of these caregivers are women, and about one in three are 65 or older themselves. Roughly 60 percent report high or very high emotional stress, and about 40 percent report high or very high physical stress [1]. In Alabama specifically, the report lists over 200,000 dementia caregivers.

This is the audience. They are not early adopters. Many of them use a phone for calls and text messages and little else. The Pew Research Center has tracked adoption of technology among older Americans for years. In their most recent reports, smartphone ownership among Americans 65 and older has climbed above 60 percent, but only around 45 percent say they feel very or somewhat confident using their devices to accomplish tasks they need to do [2]. That gap between ownership and confidence matters more than the adoption number itself.

For a caregiver who is tired, stressed, and not confident with technology, the difference between a form that takes two minutes and a form that takes twenty minutes is the difference between contributing a useful resource and giving up.

---

## Why accessibility is load-bearing here

Accessibility is sometimes treated as a compliance checkbox, a thing you do because a lawyer told you to. For this project it is the opposite. The interface is the product. If the form is hard to fill out, the directory gets no submissions. If the directory has no submissions, the chatbot has nothing to recommend. If the chatbot has nothing to recommend, Dr. Anderson's research question cannot be answered. Every feature downstream depends on the form being usable by the people who actually have the information we need.

Research on older adult usability supports the same design moves that WCAG 2.1 Level AA asks for, but for reasons that go beyond compliance.

- **Visual decline is nearly universal after 45.** Presbyopia affects an estimated 1.8 billion people worldwide, and contrast sensitivity drops measurably in nearly every person over 60 even without disease [3]. Small text and low-contrast color combinations are not cosmetic decisions for this audience. They are barriers.
- **Motor control decline is subtle but pervasive.** Studies of pointing accuracy in older adults show longer selection times and higher error rates as target size shrinks, with a sharp falloff below about 44 pixels on touchscreens [4]. This is why the Sprint 2 guide sets a 48-pixel minimum. It is not an opinion.
- **Cognitive load is the hidden cost.** Older users are not slower at everything. They are specifically slower when a task requires holding several pieces of unrelated information in working memory at once. A form that forces a user to remember information from an earlier section while filling out a later one imposes exactly this kind of cost [5].

These three findings, taken together, point at the same design: large targets, plain language, short visible sections, and no dependency on memory that the interface could store for the user.

---

## WCAG 2.1 Level AA as the baseline

WCAG 2.1 is the Web Content Accessibility Guidelines published by the W3C. Level AA is the conformance level most organizations target because it catches the accessibility issues that affect real users without requiring the stricter (and sometimes impractical) Level AAA rules [6]. Federal agencies are required to meet Level AA under Section 508. Most states follow the same standard.

For the Carebot crowdsourcing module, we chose Level AA as the floor, not the ceiling. Every rule in the Sprint 2 accessibility guide is at least Level AA, and several are stricter. Specifically:

- **Contrast ratio.** WCAG 2.1 requires 4.5:1 for normal body text. Our palette exceeds this for every text-on-background combination used in the crowdsourcing pages.
- **Touch target size.** WCAG 2.1 Success Criterion 2.5.5 sets 44x44 CSS pixels as a minimum. Our rule is 48x48, and we specify 56 pixels of height for primary action buttons.
- **Form labels.** Every form input has a visible `<label>` element, not just a placeholder. Placeholders alone fail SC 3.3.2 because the label disappears as soon as the user starts typing, which is the exact moment older users need it most.
- **Color alone is never used to convey meaning.** Status badges carry both a color and a text label. This satisfies SC 1.4.1 and, more importantly, makes the badges legible to users with color vision deficiency, which affects around 8 percent of men [7].

---

## How our design decisions map to the research

The Sprint 2 guide is the rulebook. The research below is the reason each rule is in the book.

| Design rule | Research basis | What happens without it |
|---|---|---|
| 48px minimum touch target | Studies of pointing accuracy in older adults [4], WCAG SC 2.5.5 | Users misfire repeatedly, lose confidence, abandon the form |
| 16px minimum body text | Contrast sensitivity and acuity decline with age [3], WCAG SC 1.4.4 | Users have to zoom, which breaks layouts and disorients |
| 4.5:1 contrast ratio | Same as above, plus WCAG SC 1.4.3 | Text is legible in good light but invisible at a kitchen table |
| Visible labels on every input | Working memory findings in older users [5], WCAG SC 3.3.2 | Users forget what a field was for once they start typing |
| Plain language, not jargon | Literature on health literacy in older adults | Users bounce at the first unfamiliar word |
| Short sections, one topic each | Cognitive load findings [5] | The form feels overwhelming and gets abandoned |
| No required timers or forced logouts | WCAG SC 2.2.1 | Users lose work mid-session if interrupted |

The table is not exhaustive. It is the set of rules that matter most for the population we are actually serving.

---

## Gaps in our current approach

The Sprint 2 guide and the Sprint 3 implementation are solid on the mechanical rules: sizes, contrast, labels, semantics. The gaps are in the areas that are harder to measure.

**We have not done user testing with actual older adults.** Everything in this document is grounded in published research, not in sessions we ran ourselves. That is a legitimate limitation. Research tells us what tends to be true for a population. It does not tell us what will be true for the specific people in Tuscaloosa County who open this form next week. A single round of testing with five caregivers would catch problems that research alone cannot predict.

**We have not tested with assistive technology beyond the developer's own screen reader.** VoiceOver passes on the public submit form are useful. They are not a replacement for testing with a user who actually relies on a screen reader every day. The form fields are labeled correctly, but we do not know whether the overall flow makes sense when read top to bottom by someone who cannot see the visual structure.

**We have not addressed language.** The form and the guide are English-only. A significant share of Alabama's caregiver population, including communities served by Carebot's intended users, speaks Spanish at home. The submit form has a Languages field for the provider but no Spanish-language version of itself. This is a known gap, not an oversight, and it is out of scope for the current sprint.

**We have not built in error recovery for browser crashes or timeouts.** The form is a single long page with no draft saving. If a user closes the tab or loses their connection partway through, they lose everything they typed. This is a common failure mode for the exact user population we are designing for, and it is one of the bigger usability risks still in the product.

---

## Recommendations for future work

In order of impact, the things worth doing after this sprint:

1. **Run a short round of moderated usability sessions with three to five caregivers**, ideally in the 55+ age range. Ask them to submit a real resource they know about. Watch where they stumble. Fix those things.
2. **Add a Spanish translation of the submit form and the user guide.** Django has solid internationalization support. This is a project that could be handed off to a student with translation skills.
3. **Add draft auto-save to the submit form.** Even a simple localStorage-based draft that restores what the user had typed if they come back is a huge usability win for a multi-section form.
4. **Commission one professional accessibility audit** of the live deployed site, not just the local build. Real axe-core or Deque audits on a production domain catch issues that local testing misses.
5. **Write a short plain-language privacy statement** specifically for the contact section of the submit form. The current form says the information is "optional" but does not explain what happens to it. Older users are rightly cautious about giving out personal information online.

---

## Closing note

Accessibility work is unglamorous. It does not ship as a screenshot. Most of the time, if it is done well, nobody notices it. The test of whether it worked is whether a caregiver in Prattville who has never used a web form before can finish this one without getting frustrated and closing the tab. We cannot test that from the computer lab. What we can do is follow the rules that published research and the W3C say will help the most people, and be honest about what we did not test.

---

## References

[1] Alzheimer's Association. *2024 Alzheimer's Disease Facts and Figures*. Alzheimer's & Dementia, 2024. https://www.alz.org/alzheimers-dementia/facts-figures

[2] Pew Research Center. *Mobile Fact Sheet* and related reports on technology adoption among older Americans. https://www.pewresearch.org/internet/fact-sheet/mobile/

[3] Owsley, C. "Aging and vision." *Vision Research*, vol. 51, no. 13, 2011, pp. 1610-1622. Peer-reviewed overview of age-related vision change.

[4] Findlater, L., Froehlich, J. E., Fattal, K., Wobbrock, J. O., and Dastyar, T. "Age-related differences in performance with touchscreens compared to traditional mouse input." *Proceedings of the ACM CHI Conference on Human Factors in Computing Systems*, 2013.

[5] Fisk, A. D., Rogers, W. A., Charness, N., Czaja, S. J., and Sharit, J. *Designing for Older Adults: Principles and Creative Human Factors Approaches*. CRC Press, 2009. Standard reference on age-inclusive design.

[6] World Wide Web Consortium. *Web Content Accessibility Guidelines (WCAG) 2.1*. W3C Recommendation, 2018. https://www.w3.org/TR/WCAG21/

[7] National Eye Institute. *Color blindness: Facts and statistics*. https://www.nei.nih.gov/learn-about-eye-health/eye-conditions-and-diseases/color-blindness

---

*For this assignment's preparation, the author has utilized Claude (Claude Code), a large language model created by Anthropic. Within this assignment, Claude was used for drafting assistance, outlining, and formatting. All research claims and citations were verified by the author against primary sources before submission.*
