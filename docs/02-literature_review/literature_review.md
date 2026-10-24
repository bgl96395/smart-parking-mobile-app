# DIP-3. Literature Review

**Project:** Smart Parking — booking parking spaces near business centers in Astana, with entry by license plate.

**Team:** Ansar (backend, architecture), Bigali (frontend, machine learning).

---

## 1. Scope and method

This review answers three questions:

1. **What approaches exist** to help drivers find and secure parking, and to help operators manage it?
2. **What is already known** about how well these approaches work?
3. **Which limitations** does our project need to take into account?

**Search areas.** The project combines several technical and business topics, so sources were selected in seven areas: the economics of searching for parking; smart parking system architectures; vision-based occupancy detection; parking availability prediction; reservation and pricing mechanisms; automatic license plate recognition (ALPR); privacy and legal constraints on plate data.

**Where sources come from.** IEEE Xplore, Elsevier (ScienceDirect), arXiv, and the Transportation Research Board's TRID database. Bibliographic details (authors, venue, year, DOI) were checked against publisher pages, indexes, or author repositories.

**Selection criteria.**
- Peer-reviewed journal and conference papers are preferred. One arXiv preprint is included and labeled as such.
- For fast-moving topics (deep learning for occupancy prediction and ALPR), sources from 2021–2025 are the core.
- Older works are kept where they are foundational: they define the problem (Shoup, 2006), the standard benchmark datasets (PKLot, CNRPark-EXT), or the main empirical evidence on pricing and reservations.
- Each source must directly inform a design decision or a limitation of our project.

**Result.** 15 sources: 14 scientific publications and 1 legal act.

---

## 2. Approaches and what is known

### 2.1 Why parking search is a problem

Shoup [1] formalized **cruising for parking**: drivers circle in search of cheap curb parking instead of paying for off-street parking. His model predicts that cruising is more likely when curb parking is cheap relative to off-street parking, fuel is cheap, the stay is long, and the driver values time savings low. The paper argued that cruising is a largely overlooked source of urban congestion.

**Relevance.** This is the base economic argument for our product: any time a driver spends searching is a cost, and a guaranteed space removes the search entirely. It also suggests that **price** matters. If an unreserved alternative is much cheaper, some drivers will still prefer to search rather than book.

### 2.2 Smart parking systems: architectures and sensing

Lin, Rivano and Le Mouël [2] surveyed smart parking literature from 2000 to 2016. They group work into three macro-themes: **information collection** (sensing), **system deployment** (software, algorithms, reservation, pricing), and **service dissemination** (how information reaches drivers).

Fahim, Hasan and Chowdhury [3] compared smart parking systems by technology, sensors, networking, user interface, computational methods and services, and assessed how suitable each sensing option is under different environmental conditions.

Channamallu et al. [4] reviewed 124 publications and found that:
- IoT-based systems are spreading rapidly in cities;
- wireless sensor networks are popular because they reduce wiring and deployment cost;
- camera-based detection needs fewer sensors but has high installation cost because of the camera network;
- systems based on vehicular ad hoc networks are adopted slowly because of cost and the need for on-board units.

**Relevance.** The literature treats sensing as the expensive, site-specific part of any parking system. For a pilot at one business center, installing new per-space sensors is costly. Our design therefore **reuses the partner's existing access hardware** (entry camera and barrier) and separates two different kinds of information: confirmed future bookings and current occupancy.

### 2.3 Vision-based occupancy detection

De Almeida et al. [5] released **PKLot**, a large dataset of parking-space images under sunny, overcast and rainy conditions. It became the standard benchmark. Texture-based classifiers (LBP / LPQ with SVM) exceeded 99% accuracy when trained and tested on the same camera view. On unseen parking lots, the best result fell to about 89%.

Amato et al. [6] proposed a compact CNN for **smart cameras** that matches AlexNet on occupancy detection while being three orders of magnitude smaller. They also released **CNRPark-EXT**, which covers multiple seasons, occlusions and difficult viewpoints. They report that their approach outperforms and generalizes better than earlier methods on both datasets.

Martynova et al. [7] (arXiv preprint) revisited deep learning methods for parking lot occupancy detection using recent architectures and datasets.

**What is known.** Same-view accuracy is high and mostly solved. The hard problem is **generalization**: to new lots, camera angles, weather and occlusion.

**Relevance.** Astana has long, snowy winters. Snow cover, low sun and covered lots are conditions that public benchmarks represent poorly. We cannot assume a published accuracy figure will hold at our site without local validation. This is one reason our product shows an explicit **"unknown"** status instead of guessing.

### 2.4 Parking availability prediction

Xiao et al. [8] present what they describe as the first survey dedicated to **parking prediction** methods in smart cities. They argue that accurate availability prediction reduces search time and congestion, and cover both classical machine learning and deep learning approaches.

Channamallu et al. [9] reviewed 108 studies on occupancy prediction. Their findings:
- **hybrid models** that combine approaches are the most accurate;
- CNNs and RNNs capture complex spatial and temporal patterns;
- **LSTM** models are promising for short-term availability;
- ARIMA-type time series models remain suitable for regular temporal patterns.

**What is known.** Prediction works best with long, clean historical data and regular demand patterns.

**Relevance.** A new pilot site has **no history**, so prediction cannot be a launch feature. It is planned as a later ML module (Bigali) once the platform has collected entry/exit logs and status history. Until then, the platform shows only observed data with its source and age.

### 2.5 Reservation and pricing

Kaspi et al. [10] studied **parking reservation policies** in vehicle-sharing systems, measuring total excess travel time caused by vehicle or space shortages. Two findings matter for us:
- reservations improve system performance even when only a small share of users is required to reserve;
- **overbooking** parking spaces is unlikely to be beneficial.

Millard-Ball, Weinberger and Hampshire [11] evaluated **SFpark**, San Francisco's demand-responsive pricing program, which targeted 60–80% occupancy on metered blocks. In the first two years, rate changes helped reach the occupancy goal and **reduced cruising by about 50%**.

**What is known.** Two different levers reduce search: reservations, which give certainty to the individual driver, and pricing, which keeps some capacity free for everyone.

**Relevance.** Our product relies on reservations from a **partner-allocated quota**. Following [10], we do not overbook: the core promise is that a booked driver gets a space. Dynamic pricing [11] is out of scope for the pilot, because prices are set by the parking operator under contract. It may become relevant later.

### 2.6 Automatic license plate recognition (ALPR)

Laroca et al. [12] proposed an ALPR system built on the **YOLO** detector. It handles plate detection and layout classification, and reaches about 96.8% average end-to-end recognition across eight public datasets from five regions. It runs in real time on a high-end GPU and outperformed previous work and commercial systems on several benchmarks.

Laroca et al. [13] then showed that popular ALPR datasets contain **near-duplicates**: different images of the same plate in both training and test sets. When six well-known models were retrained and tested on duplicate-free splits, recognition accuracy dropped noticeably, and on one dataset the ranking of models changed.

**What is known.** Modern ALPR is fast and accurate on benchmarks, but published numbers may overstate real-world accuracy. Plate layouts also differ by country, so models need data for the target region.

**Relevance.** Kazakhstan plate formats are not the focus of the main public datasets. Entry by plate is therefore treated as a component that **can fail**. Every entry must have a **manual fallback** through the operator, and every failure should be logged so accuracy can be measured on our own data rather than taken from papers.

### 2.7 Privacy and legal constraints

Woods [14] analyzed ANPR under European human rights law. The paper argues that ANPR and the retention of ANPR data interfere with the right to privacy, and that a regime without a clear legal basis would likely fail a proportionality assessment.

In Kazakhstan, the **Law "On Personal Data and Their Protection" No. 94-V** [15] requires consent before collecting personal data, limits collection and storage to what the stated purpose requires, and, since a 2015 amendment, requires personal databases to be kept on the territory of Kazakhstan.

**Relevance.** A license plate linked to an account and trip history is personal data. The platform must collect **explicit consent**, store only what booking and entry require, define a **retention period** for entry/exit logs, and host personal data **inside Kazakhstan**. This is a direct backend and architecture constraint.

---

## 3. Analysis table

| # | Source | Year | Type | Approach / focus | Key findings | Limitations | Use in our project |
|---|---|---|---|---|---|---|---|
| 1 | Shoup, *Transport Policy* | 2006 | Journal (foundational) | Economic model of cruising for parking | Cruising rises when curb parking is cheap relative to off-street parking; it is an overlooked source of congestion | Theoretical model, curb parking focus, US context | Core problem statement; price affects whether drivers book |
| 2 | Lin, Rivano, Le Mouël, *IEEE T-ITS* | 2017 | Journal survey (foundational) | Taxonomy of smart parking research 2000–2016 | Three themes: information collection, system deployment, service dissemination | Pre-dates recent deep learning work | Structure of our architecture: sensing, booking logic, driver app |
| 3 | Fahim, Hasan, Chowdhury, *Heliyon* | 2021 | Journal review | Comparison of sensors, networks, UI, methods | Sensing options differ in cost and suitability for environmental conditions | Broad overview, no field evaluation | Choosing to reuse existing site hardware instead of new sensors |
| 4 | Channamallu et al., *Transportation Research Procedia* | 2023 | Conference review (124 sources) | IoT-based smart parking systems | WSNs reduce deployment cost; cameras need fewer sensors but cost more to install; VANET systems adopt slowly | Literature-based, no cost data for specific sites | Justifies a pilot built on existing cameras and barriers |
| 5 | de Almeida et al., *Expert Systems with Applications* | 2015 | Journal + dataset (foundational) | PKLot dataset; texture features + SVM | >99% accuracy on the same view, ~89% on unseen lots | Few lots, no snow, older methods | Shows generalization is the main risk for vision sensing |
| 6 | Amato et al., *Expert Systems with Applications* | 2017 | Journal + dataset | Compact CNN on smart cameras; CNRPark-EXT | AlexNet-level accuracy at a fraction of the size; better generalization | European conditions; dataset from one site | Feasibility of on-camera occupancy detection as a future ML module |
| 7 | Martynova et al., arXiv | 2023 (v3 2024) | Preprint | Revisits deep learning for occupancy detection | Re-evaluates recent architectures on current datasets | Not peer-reviewed | Starting point for choosing occupancy models later |
| 8 | Xiao et al., *IEEE T-ITS* | 2023 | Journal survey | Parking availability prediction methods | First dedicated survey; ML and DL both used; prediction reduces search time | Survey only, depends on data availability in cited works | Roadmap for the prediction module |
| 9 | Channamallu et al., *Transportation Research Procedia* | 2023 | Review (108 studies) | Occupancy prediction models | Hybrid models most accurate; LSTM good for short-term; ARIMA for regular patterns | Requires historical data | Prediction postponed until pilot data exists |
| 10 | Kaspi et al., *European Journal of Operational Research* | 2016 | Journal | Parking reservation policies (vehicle sharing) | Reservations help even with partial adoption; overbooking unlikely to help | Vehicle-sharing context, not business-center parking | No overbooking; quota-based guaranteed bookings |
| 11 | Millard-Ball, Weinberger, Hampshire, *Transportation Research Part A* | 2014 | Journal (empirical) | Demand-responsive pricing (SFpark) | Rate changes met occupancy goals and cut cruising by ~50% | On-street parking in one US city | Pricing as a later lever; out of pilot scope |
| 12 | Laroca et al., *IET Intelligent Transport Systems* | 2021 | Journal | YOLO-based layout-independent ALPR | ~96.8% end-to-end recognition across 8 datasets; real time | Benchmarks do not cover Kazakhstan plates | Reference design if the site lacks a usable ALPR camera |
| 13 | Laroca et al., *IJCNN* | 2023 | Conference | Dataset bias in license plate recognition | Near-duplicates inflate accuracy; fair splits lower scores and change model ranking | Studies two datasets | Do not trust benchmark accuracy; measure on our own data; manual fallback |
| 14 | Woods, *Journal of Information Rights, Policy and Practice* | 2017 | Journal (legal) | ANPR and data retention under European law | ANPR retention interferes with privacy; needs clear legal basis and proportionality | European legal framework | Minimize and time-limit plate data |
| 15 | Law of RK No. 94-V | 2013 (amended) | Legal act | Personal data in Kazakhstan | Consent required; purpose limitation; local storage of personal databases | Legal text, not research | Consent flow, retention policy, hosting in Kazakhstan |

---

## 4. Synthesis: what is already known

1. **Searching for parking is costly**, and both reservations and pricing can reduce it [1, 10, 11].
2. **Sensing is the expensive, site-specific part** of smart parking. Cameras reduce sensor count but raise installation cost [2, 3, 4].
3. **Vision-based occupancy detection is accurate on known views** but generalizes poorly to new sites and conditions [5, 6, 7].
4. **Availability prediction works** when historical data is available; hybrid and LSTM models lead [8, 9].
5. **Reservations work without overbooking**, even with partial adoption [10].
6. **ALPR is mature on benchmarks**, but benchmark accuracy can be inflated and does not transfer automatically to new plate formats [12, 13].
7. **Plate data is personal data** and is regulated: consent, minimization, retention and local storage [14, 15].

**Gap.** Most work focuses on one component: sensing, prediction or recognition. Fewer studies address the operational side that decides whether a booking is actually fulfilled at the gate: integration with existing access systems, partner-allocated quotas, temporary closures, manual fallback, and honest reporting of data freshness. This is where our project focuses.

---

## 5. Limitations the project takes into account

| Limitation from the literature | How the project handles it |
|---|---|
| Occupancy detection generalizes poorly to new sites and weather [5, 6] | Status shows source and update time; stale or missing data is shown as **"unknown"**, not guessed |
| Prediction needs historical data [8, 9] | No prediction at launch; collect logs during the pilot, add the ML module later |
| Overbooking undermines reservations [10] | Bookings come only from a fixed **partner quota**; no overbooking |
| ALPR accuracy may be lower than reported [13]; local plate formats differ [12] | Plate entry has a **manual operator fallback**; every recognition failure is logged and measured |
| New sensors are expensive to deploy [4] | Pilot reuses the partner's existing camera and barrier after a compatibility check |
| Plate data is personal and regulated [14, 15] | Explicit consent, minimal data, defined retention period, hosting in Kazakhstan |
| Price affects booking vs. searching [1, 11] | Driver sees the full price before payment; pricing strategy is set after the pilot |
| Most evidence comes from Europe, the US and Brazil | Winter conditions and local behaviour in Astana are validated in our own pilot, not assumed |

---

## 6. References

1. Shoup, D. C. (2006). Cruising for parking. *Transport Policy*, 13(6), 479–486. https://doi.org/10.1016/j.tranpol.2006.05.005
2. Lin, T., Rivano, H., & Le Mouël, F. (2017). A survey of smart parking solutions. *IEEE Transactions on Intelligent Transportation Systems*, 18(12), 3229–3253. https://doi.org/10.1109/TITS.2017.2685143
3. Fahim, A., Hasan, M., & Chowdhury, M. A. (2021). Smart parking systems: comprehensive review based on various aspects. *Heliyon*, 7(5), e07050. https://doi.org/10.1016/j.heliyon.2021.e07050
4. Channamallu, S. S., Kermanshachi, S., Rosenberger, J. M., & Pamidimukkala, A. (2023). A review of smart parking systems. *Transportation Research Procedia*, 73, 289–296. https://doi.org/10.1016/j.trpro.2023.11.920
5. de Almeida, P. R. L., Oliveira, L. S., Britto, A. S., Silva, E. J., & Koerich, A. L. (2015). PKLot – A robust dataset for parking lot classification. *Expert Systems with Applications*, 42(11), 4937–4949. https://doi.org/10.1016/j.eswa.2015.02.009
6. Amato, G., Carrara, F., Falchi, F., Gennaro, C., Meghini, C., & Vairo, C. (2017). Deep learning for decentralized parking lot occupancy detection. *Expert Systems with Applications*, 72, 327–334. https://doi.org/10.1016/j.eswa.2016.10.055
7. Martynova, A., Kuznetsov, M., Porvatov, V., Tishin, V., Kuznetsov, A., Semenova, N., & Kuznetsova, K. (2023). Revising deep learning methods in parking lot occupancy detection. *arXiv preprint* arXiv:2306.04288. https://arxiv.org/abs/2306.04288
8. Xiao, X., Peng, Z., Lin, Y., Jin, Z., Shao, W., Chen, R., Cheng, N., & Mao, G. (2023). Parking prediction in smart cities: A survey. *IEEE Transactions on Intelligent Transportation Systems*, 24(10), 10302–10326. https://doi.org/10.1109/TITS.2023.3279024
9. Channamallu, S. S., Kermanshachi, S., Rosenberger, J. M., & Pamidimukkala, A. (2023). Parking occupancy prediction and analysis – a comprehensive study. *Transportation Research Procedia*, 73, 297–304. https://doi.org/10.1016/j.trpro.2023.11.921
10. Kaspi, M., Raviv, T., Tzur, M., & Galili, H. (2016). Regulating vehicle sharing systems through parking reservation policies: Analysis and performance bounds. *European Journal of Operational Research*, 251(3), 969–987. https://doi.org/10.1016/j.ejor.2015.12.015
11. Millard-Ball, A., Weinberger, R. R., & Hampshire, R. C. (2014). Is the curb 80% full or 20% empty? Assessing the impacts of San Francisco's parking pricing experiment. *Transportation Research Part A: Policy and Practice*, 63, 76–92. https://doi.org/10.1016/j.tra.2014.02.016
12. Laroca, R., Zanlorensi, L. A., Gonçalves, G. R., Todt, E., Schwartz, W. R., & Menotti, D. (2021). An efficient and layout-independent automatic license plate recognition system based on the YOLO detector. *IET Intelligent Transport Systems*, 15(4), 483–503. https://doi.org/10.1049/itr2.12030
13. Laroca, R., Estevam, V., Britto, A. S., Minetto, R., & Menotti, D. (2023). Do we train on test data? The impact of near-duplicates on license plate recognition. In *2023 International Joint Conference on Neural Networks (IJCNN)*, 1–8. https://doi.org/10.1109/IJCNN54540.2023.10191584
14. Woods, L. (2017). Automated number plate recognition: Data retention and the protection of privacy in public places. *Journal of Information Rights, Policy and Practice*, 2(1). https://doi.org/10.21039/irpandp.v2i1.35
15. Law of the Republic of Kazakhstan No. 94-V "On Personal Data and Their Protection" of 21 May 2013 (as amended). https://adilet.zan.kz/eng/docs/Z1300000094
