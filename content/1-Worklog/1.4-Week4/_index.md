---
title: "Worklog Week 4"
date: 2026-04-06
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Week 4: Dashboard & Flashcard UI

**Date:** 06/04/2026 - 12/04/2026  
**Total Time:** ~40 hours

### Week 4 Objectives:

* **Build Dashboard:** Create dashboard layout, components, widgets
* **Flashcard UI:** Implement flashcard list, review interface, SRS scheduling
* **API Integration:** Connect frontend with backend APIs
* **State Management:** Setup Redux/Context for app state
* **Deployment:** Deploy frontend to AWS Amplify
* **Testing:** Test responsive design, API integration
* **Support Backend:** Participate in discussions about Flashcard use cases

### Work Content:

| Day | Task | Status | References |
| --- | --- | --- | --- |
| **Monday** | **Build Dashboard Layout** <br> - Create dashboard page layout <br> - Create header, sidebar, main content area <br> - Implement responsive design <br> - Style with Tailwind CSS | ✅ Completed | [Next.js Layouts](https://nextjs.org/docs/app/building-your-application/routing/layouts-and-templates) |
| **Tuesday** | **Create Dashboard Widgets** <br> - Create stats widgets (total cards, due today, retention) <br> - Create charts with recharts library <br> - Implement data visualization <br> - Style widgets with Shadcn/ui | ✅ Completed | [Recharts](https://recharts.org/)<br>[Shadcn/ui Cards](https://ui.shadcn.com/docs/components/card) |
| **Wednesday** | **Implement Flashcard List & Review UI** <br> - Create flashcard list page <br> - Implement card flip animation <br> - Create review interface (Again/Hard/Good/Easy buttons) <br> - Implement keyboard shortcuts | ✅ Completed | [React Animations](https://www.framer.com/motion/) |
| **Thursday** | **Setup State Management** <br> - Configure Redux Toolkit or Context API <br> - Create slices for user, flashcards, stats <br> - Implement state persistence <br> - Create custom hooks | ✅ Completed | [Redux Toolkit](https://redux-toolkit.js.org/) |
| **Friday** | **API Integration & Deployment** <br> - Create API client (axios wrapper) <br> - Integrate dashboard with backend APIs <br> - Integrate flashcard with backend APIs <br> - Deploy frontend to AWS Amplify | ✅ Completed | [AWS Amplify](https://docs.aws.amazon.com/amplify/) |
| **Sunday** | **Testing & Support Backend** <br> - Test responsive design (desktop, mobile, tablet) <br> - Test API integration <br> - Participate in discussions about Flashcard use cases <br> - Take notes on feedback from testing | ✅ Completed | [Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design) |

### 🎓 Knowledge Learned:

| Topic | Content | Result |
|---------|---------|--------|
| **Dashboard Design** | Layout, widgets, responsive | ✅ Know how to build dashboard |
| **Data Visualization** | Charts, graphs | ✅ Know how to use recharts |
| **Animations** | Card flip, transitions | ✅ Know how to create animations |
| **State Management** | Redux, Context API | ✅ Know how to manage state |
| **API Integration** | Axios, error handling | ✅ Know how to connect APIs |
| **Deployment** | Amplify, CI/CD | ✅ Know how to deploy frontend |
| **Responsive Design** | Mobile-first, breakpoints | ✅ Know how to design responsive |

### Results:

* **Dashboard:**
  * ✅ Dashboard layout complete
  * ✅ Stats widgets display correct data
  * ✅ Charts visualize learning progress
  * ✅ Responsive on desktop, tablet, mobile

* **Flashcard UI:**
  * ✅ Flashcard list page working
  * ✅ Card flip animation smooth
  * ✅ Review interface intuitive
  * ✅ Keyboard shortcuts working

* **State Management:**
  * ✅ Redux store configured
  * ✅ Slices for user, flashcards, stats
  * ✅ State persistence working
  * ✅ Custom hooks created

* **API Integration:**
  * ✅ API client setup
  * ✅ Dashboard APIs integrated
  * ✅ Flashcard APIs integrated
  * ✅ Error handling implemented

* **Deployment:**
  * ✅ Frontend deployed to AWS Amplify
  * ✅ CI/CD pipeline working
  * ✅ Environment variables configured
  * ✅ Production build optimized

### Lessons Learned:

* **Lesson 1: Responsive Design from Start** - Designing responsive from the beginning is much easier than adding it later.

* **Lesson 2: State Management Helps Code Maintainability** - Redux Toolkit helps manage complex state in an organized way.

* **Lesson 3: API Integration Needs Error Handling** - Handling API errors correctly improves UX.

### Week 5 Plan:

* Integrate Amazon Bedrock for AI Tutor
* Implement conversation UI
* Implement streaming responses
* Test end-to-end conversation flow
* Participate in team review meeting

---

## 📊 Week 4 Statistics:

| Criteria | Value |
|----------|-------|
| **Total Time** | ~40 hours |
| **Working Days** | 6 days |
| **Components Created** | 10+ components |
| **APIs Integrated** | 5+ APIs |

---

**Updated:** 29/04/2026  
**Status:** ✅ Completed
