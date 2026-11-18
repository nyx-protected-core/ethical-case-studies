# Sources: safe drinking water and sanitation case study

Status date: 18 Nov 2026.

## Method

- **DOIs:** each DOI was resolved through doi.org (HTTP 302 redirect confirmed). Its title, authors, venue and year were then checked against Crossref metadata. Abstracts were read from Crossref, OpenAlex, Semantic Scholar or Europe PMC, and every figure taken from an abstract was checked against that text.
- **Web pages and PDFs:** each was fetched directly (curl, or a web-fetch service where the site blocked curl), and the cited figures were found in the fetched text.
- **Labels in README.md:**
  - **(unverified)**: claims we could not confirm.
  - **(attributed)**: figures a verified source reports from a study we did not open.
  - **(our arithmetic)**: our own calculations.
- No figure, quote or DOI was taken from memory or from search-engine summaries alone.

## Counts

- Cited sources: 59.
  - Verified: 59. For DOIs, doi.org and Crossref both checked and the content read; for links, fetched and the figures found.
  - Partly verified: 0.
  - Unverified: 0.
- Claims in the text explicitly labelled unverified: 3:
  - Manila's later contract history;
  - the status of the PAM Jaya conversion and IPO at 18 Nov 2026;
  - independent Jal Jeevan Mission functionality data.
- Attributed claims: 2:
  - the remunicipalisation count, via [S45];
  - the $114 billion estimate, read in [S6] and attributed to [S4].
- Checked but not cited: 9 (table at the end).

## Cited sources

| # | Source | Type | Identifier | Used for | Check |
| --- | --- | --- | --- | --- | --- |
| S1 | WHO/UNICEF JMP (2025), Progress on household drinking water, sanitation and hygiene 2000-2024: special focus on inequalities | Intergovernmental report | https://cdn.who.int/media/docs/default-source/wash-documents/wash-coverage/jmp/jmp-2025-wash-households-lowres-launch.pdf | 2024 coverage figures; eightfold, sixfold, threefold, sevenfold and 18-fold multiples; 2030 projections | PDF fetched, figures found |
| S2 | WHO news release, 26 Aug 2025, "1 in 4 people globally still lack access to safe drinking water" | Intergovernmental news | https://www.who.int/news/item/26-08-2025-1-in-4-people-globally-still-lack-access-to-safe-drinking-water---who--unicef | Publication date; 1 in 4, 2.1 billion | Fetched |
| S3 | JMP report page, washdata.org | Intergovernmental web page | https://washdata.org/reports/jmp-2025-wash-households | Current household report as of 18 Nov 2026 | Fetched 18 Nov 2026 |
| S4 | Hutton & Varughese (2016), The Costs of Meeting the 2030 Sustainable Development Goal Targets on Drinking Water, Sanitation, and Hygiene, World Bank | Report | doi:10.1596/k8543 | $114 billion a year (read via S6) | doi.org and Crossref OK |
| S5 | Rozenberg & Fay (2019), Beyond the Gap, World Bank | Report | doi:10.1596/978-1-4648-1363-4 | 2–8% of GDP; 4.5% with policy mix | doi.org and Crossref OK, abstract read |
| S6 | Andres et al. (2019), Doing More with Less: Smarter Subsidies for Water Supply and Sanitation, World Bank | Report | doi:10.1596/32277; https://documents1.worldbank.org/curated/en/330841560517317845/pdf/Doing-More-with-Less-Smarter-Subsidies-for-Water-Supply-and-Sanitation.pdf | 56% vs 6%; $289–353bn and about $320bn; 35% of utilities cover O&M; IBT mistargeting; 90–100% errors; cites $114bn | doi.org and Crossref OK; PDF fetched |
| S7 | Fuente et al. (2016), Water and sanitation service delivery, pricing, and the poor: An empirical estimate of subsidy incidence in Nairobi, Kenya, Water Resources Research 52(6) | Peer-reviewed | doi:10.1002/2015WR018375 | IBT subsidies favour high-income and non-residential customers | doi.org and Crossref OK, abstract read |
| S8 | Biswas & Tortajada (2010), Water Supply of Phnom Penh: An Example of Good Governance, IJWRD 26(2) | Peer-reviewed | doi:10.1080/07900621003768859 | Unaccounted-for water 72% to 6.19%; production, network and customer growth | doi.org and Crossref OK, abstract read |
| S9 | Chan (2009), Bringing Safe Water to Phnom Penh's City, IJWRD 25(4) | Peer-reviewed | doi:10.1080/07900620903306323 | NRW 6.15%; collection over 99%; pro-poor subsidies | doi.org and Crossref OK, abstract read |
| S10 | Araral (2008), Public Provision for Urban Water: Getting Prices and Governance Right, Governance 21(4) | Peer-reviewed | doi:10.1111/j.1468-0491.2008.00412.x | Governance explanation of PPWSA | doi.org and Crossref OK, abstract read |
| S11 | Biswas & Tortajada (2009), Water Supply of Phnom Penh: A Most Remarkable Transformation, Third World Centre for Water Management | Research report | https://thirdworldcentre.org/wp-content/uploads/2020/08/Water-Supply-of-Phnom-Penh-A-Most-Remarkable-Transformation.pdf | 72% to about 6.2%; resellers charging 10 to 20 times the subsidised rate | PDF fetched, quote found |
| S12 | GPRBA Annual Report 2025 | Multilateral report | https://www.gprba.org/sites/default/files/publication/downloads/2025-11/GPRBA_Annual_Report_2025.pdf | Founded 2003; 60 projects, 31 countries plus one territory; $241.7m disbursed; water and sanitation 29% | Fetched (web-fetch), figures found |
| S13 | GPRBA (2020), Bangladesh OBA Sanitation Microfinance Program, RBF case study | Multilateral case study | https://www.gprba.org/sites/default/files/publication/downloads/2020-09/Bangladesh-sanitation-GPRBA-case-study.pdf | OBA definition; 170,679 latrines; 14,448 sold without loans; targeting caveat; blended approach | Fetched (web-fetch), figures found |
| S14 | OECD (2019), Making Blended Finance Work for Water and Sanitation | Intergovernmental report | https://www.oecd.org/content/dam/oecd/en/publications/reports/2019/08/making-blended-finance-work-for-water-and-sanitation_ce59dbca/5efc8950-en.pdf | USD 2.1bn of USD 157.2bn (1.36%), 2012–17 | PDF fetched, figures found |
| S15 | Pickering et al. (2015), Effect of a community-led sanitation intervention on child diarrhoea and child growth in rural Mali: a cluster-randomised controlled trial, Lancet Glob Health 3(11) | Peer-reviewed | doi:10.1016/S2214-109X(15)00144-8 | Latrine access, open defecation, diarrhoea, growth | doi.org and Crossref OK, abstract read |
| S16 | Orgill-Meyer et al. (2019), Long-term impact of a community-led sanitation campaign in India, 2005–2016, Bull WHO 97(8) | Peer-reviewed | doi:10.2471/BLT.18.221572 | 2010 effects; 2016 convergence and slippage | doi.org and Crossref OK, abstract read |
| S17 | Cameron, Olivia & Shah (2019), Scaling up sanitation: evidence from an RCT in Indonesia, J Dev Econ 138 | Peer-reviewed | doi:10.1016/j.jdeveco.2018.12.001 | Modest toilet effect; no growth effect; heterogeneity | doi.org and Crossref OK, abstract read |
| S18 | Luby et al. (2018), Effects of water quality, sanitation, handwashing, and nutritional interventions on diarrhoea and child growth in rural Bangladesh (WASH Benefits), Lancet Glob Health 6(3) | Peer-reviewed | doi:10.1016/S2214-109X(17)30490-4 | Diarrhoea and growth results | doi.org and Crossref OK, abstract read |
| S19 | Null et al. (2018), Effects of water quality, sanitation, handwashing, and nutritional interventions on diarrhoea and child growth in rural Kenya (WASH Benefits), Lancet Glob Health 6(3) | Peer-reviewed | doi:10.1016/S2214-109X(18)30005-6 | Null diarrhoea and growth results; adherence | doi.org and Crossref OK, abstract read |
| S20 | Humphrey et al. (2019), Independent and combined effects of improved WASH and improved complementary feeding on child stunting and anaemia in rural Zimbabwe (SHINE), Lancet Glob Health | Peer-reviewed | doi:10.1016/S2214-109X(18)30374-7 | WASH null on stunting and diarrhoea; infant feeding effect | doi.org and Crossref OK, abstract read |
| S21 | Cumming et al. (2019), The implications of three major new trials for the effect of water, sanitation and hygiene on childhood diarrhea and stunting: a consensus statement, BMC Medicine | Peer-reviewed consensus statement | doi:10.1186/s12916-019-1410-x | Interpretation; "transformative WASH" | doi.org and Crossref OK, abstract read |
| S22 | Foster, Furey, Banks & Willetts (2020), Functionality of handpump water supplies: a review, IJWRD 36(5) | Peer-reviewed | doi:10.1080/07900627.2018.1543117 | About 1 in 4 non-functional; 47 countries; about 175,000 in 2015 | doi.org and Crossref OK, abstract read |
| S23 | Foster (2013), Predictors of Sustainability for Community-Managed Handpumps in Sub-Saharan Africa: Evidence from Liberia, Sierra Leone, and Uganda, ES&T 47(21) | Peer-reviewed | doi:10.1021/es402086n | More than 25,000 handpumps in Liberia, Sierra Leone and Uganda; predictors | doi.org and Crossref OK, abstract read |
| S24 | MacAllister et al. (2022), Contribution of physical factors to handpump borehole functionality in Africa, Sci Total Environ 851 | Peer-reviewed | doi:10.1016/j.scitotenv.2022.158343 | 145 boreholes in Ethiopia, Uganda and Malawi; 19% low transmissivity; 38% cylinder depth | doi.org and Crossref OK, abstract read |
| S25 | Thomson, Hope & Foster (2012), GSM-enabled remote monitoring of rural handpumps: a proof-of-concept study, J Hydroinformatics 14(4) | Peer-reviewed | doi:10.2166/hydro.2012.183 | "One in three" estimate; GSM transmitter | doi.org and Crossref OK, abstract read |
| S26 | Koehler, Thomson & Hope (2015), Pump-Priming Payments for Sustainable Water Services in Rural Africa, World Dev 74 | Peer-reviewed | doi:10.1016/j.worlddev.2015.05.020 | About 200 million handpump users; pooling risk | doi.org and Crossref OK, abstract read |
| S27 | Papastylianou et al. (2014), Smart handpumps: technical aspects of a one-year field trial in rural Kenya, IET conference (AHT 2014) | Peer-reviewed conference paper | doi:10.1049/cp.2014.0769 | 67 pumps; outage 27 days to under 3 | doi.org and Crossref OK, abstract read |
| S28 | Thomson, Stoler, Byford & Bradley (2024), Impact of rapid handpump repairs on diarrhoea morbidity in children, Kwale County, Kenya, JMIR Public Health Surveill 10 | Peer-reviewed | doi:10.2196/42462 | 90% repaired within 3 days; aOR 0.35 only for repairs within 24 hours | doi.org and Crossref OK; full abstract read via Europe PMC |
| S29 | Smith School, University of Oxford / REACH (2022), Investing in professionalized maintenance... rural Kenya (Kitui policy brief) | Academic policy brief | https://www.smithschool.ox.ac.uk/sites/default/files/2022-02/Kitui-maintenance-policy-brief_0.pdf | Repair under 2 days vs 46–67 and 43 days; USD 0.38 a day; 6–14% of spending | PDF fetched, figures found |
| S30 | Innocenti, Brunckhorst, Katuva & Koehler (2025), Community decisions to insure against collective risk, J Dev Stud 61(9) | Peer-reviewed | doi:10.1080/00220388.2025.2474968 | 864 participants, 144 communities; contributions rise with risk | doi.org and Crossref OK, abstract read |
| S31 | UN General Assembly resolution A/RES/64/292, The human right to water and sanitation, 28 July 2010 | UN resolution | https://documents.un.org/api/symbol/access?s=A/RES/64/292&l=en&t=pdf | Recognition text and date | PDF fetched, quote found |
| S32 | UN press release GA/10967, 28 July 2010 | UN press release | https://press.un.org/en/2010/ga10967.doc.htm | 122 in favour, none against, 41 abstentions | Fetched (web-fetch) |
| S33 | UN Human Rights Council resolution A/HRC/RES/15/9, 30 Sept 2010 | UN resolution | https://documents.un.org/api/symbol/access?s=A/HRC/RES/15/9&l=en&t=pdf | "Derived from the right to an adequate standard of living"; adopted without a vote | PDF fetched, quote found |
| S34 | Nickson & Vargas (2002), The Limitations of Water Regulation: The Failure of the Cochabamba Concession, Bull Latin Am Res 21(1) | Peer-reviewed | doi:10.1111/1470-9856.00034 | 40-year concession of Sept 1999; cancelled after riots; regulatory lessons | doi.org and Crossref OK, abstract read |
| S35 | Bustamante (2004), The water war: resistance against privatisation of water in Cochabamba, REGA 1(1) | Peer-reviewed | doi:10.21168/rega.v1n1.p37-46 | Law 2029; clashes in Feb and Apr 2000; state of siege; Law 2066 | doi.org and Crossref OK, abstract read |
| S36 | Wu & Malaluan (2008), A Tale of Two Concessionaires: water privatisation in Metro Manila, Urban Studies 45(1) | Peer-reviewed | doi:10.1177/0042098007085108 | Divergent outcomes; internal factors | doi.org and Crossref OK, abstract read |
| S37 | Wolf (2009), Manila Water: Privatising Regulated Services in a Developing Country, J Strategic Management Education 5(2) (repository copy) | Academic case study | https://www.tara.tcd.ie/tara8/server/api/core/bitstreams/bf1ee2df-a5ab-48af-8688-dab8a323257c/content | About 11 million people; NRW 63% (1997) to almost 70% (2005, West Zone); Maynilad returned concession in 2005 | Fetched (web-fetch), figures found |
| S38 | Cheng (2013), (In)visible urban water networks: the politics of non-payment in Manila's low-income communities, Environment and Urbanization 25(1) | Peer-reviewed | doi:10.1177/0956247812469926 | Unequal payment-recovery treatment | doi.org and Crossref OK, abstract read |
| S39 | Tuslian, Rinwigati & Sandyawan (2024), In Pursuit of Water Justice in Jakarta, Indonesian J Socio-Legal Studies 3(2) | Peer-reviewed | doi:10.54828/ijsls.2024v3n2.3; https://scholarhub.ui.ac.id/cgi/viewcontent.cgi?article=1055&context=ijsls | Court numbers and sequence, 2012–2018 | doi.org and Crossref OK; full text fetched (web-fetch) |
| S40 | The Jakarta Post, 7 Dec 2020, "Jakarta urged to move with tap water remunicipalization plan despite court ruling" | News | https://www.thejakartapost.com/news/2020/12/07/jakarta-urged-to-move-with-tap-water-remunicipalization-plan-despite-court-ruling | 1997 25-year concessions; case review accepted 20 Nov 2018 | Fetched, text found |
| S41 | Kompas.com, 1 Feb 2023, "PAM Jaya Resmi Akhiri Swastanisasi Air Bersih di Jakarta pada Rabu Ini" | News (Indonesian) | https://megapolitan.kompas.com/read/2023/02/01/15373261/pam-jaya-resmi-akhiri-swastanisasi-air-bersih-di-jakarta-pada-rabu-ini | Contracts with Palyja and Aetra ended 1 Feb 2023 after 25 years | Fetched, text found |
| S42 | ANTARA, 14 Oct 2022, "PAM Jaya tegaskan kerja sama dengan Moya bukan swastanisasi air" | News agency (Indonesian) | https://www.antaranews.com/berita/3179597/pam-jaya-tegaskan-kerja-sama-dengan-moya-bukan-swastanisasi-air | Bundling agreement scope; 65.85% coverage; 46.67% NRW; 2030 target | Fetched (web-fetch) |
| S43 | ANTARA, 17 Jul 2025, "Rencanakan IPO, PAM Jaya sudah banyak dilirik investor" | News agency (Indonesian) | https://www.antaranews.com/berita/4973673/rencanakan-ipo-pam-jaya-sudah-banyak-dilirik-investor | IPO target 2027; conversion in process; more than 2.5 million customers | Fetched (web-fetch) |
| S44 | Bel, Fageda & Warner (2010), Is private production of public services cheaper than public production? A meta-regression analysis of solid waste and water services, JPAM 29(3) | Peer-reviewed | doi:10.1002/pam.20509 | No systematic cost savings | doi.org and Crossref OK, abstract read |
| S45 | McDonald (2018), Remunicipalization: The future of water services?, Geoforum 91 | Peer-reviewed | doi:10.1016/j.geoforum.2018.02.027; author copy https://www.queensu.ca/municipal-services-project/sites/mspwww/files/uploaded_files/Journal%20PDF/Remunicipalization%20-%20The%20Future%20of%20Water%20Services%202018.pdf | 267 cases, 37 countries, more than 100 million people (attributed); diverse motives; brakes; US about 25% | doi.org and Crossref OK; author PDF fetched |
| S46 | Eau de Paris, FAQ "What was the impact of remunicipalisation on the water bill?" | Public utility web page | https://www.eaudeparis.fr/en/faq/what-is-the-impact-of-remunicipalisation-on-the-water-bill | €35 million annual gains (operator's estimate) | Fetched, figure found |
| S47 | Jasechko et al. (2024), Rapid groundwater decline and some cases of recovery in aquifers globally, Nature 625 | Peer-reviewed | doi:10.1038/s41586-023-06879-8 | 170,000 wells; 1,693 aquifer systems; over 0.5 m a year; 30% accelerating; recoveries | doi.org and Crossref OK, abstract read |
| S48 | Flanagan, Johnston & Zheng (2012), Arsenic in tube well water in Bangladesh, Bull WHO 90(11) | Peer-reviewed | doi:10.2471/BLT.11.101253 | 20m and 45m exposed; 24,000 and perhaps 19,000 deaths; 1 in 16; US$13bn; 5m above 200 µg/L; US$11 per capita | doi.org and Crossref OK, abstract read |
| S49 | British Geological Survey, Bangladesh reports and data (DPHE/BGS National Hydrochemical Survey) | Government geological survey page | https://www.bgs.ac.uk/groundwater/quality/groundwater-and-health/arsenic-contamination-of-groundwater/bangladesh-reports/ | 3,534 boreholes in 61 of 64 districts | Fetched, text found |
| S50 | Podgorski et al. (2018), Prediction modeling and mapping of groundwater fluoride contamination throughout India, ES&T 52(17) | Peer-reviewed | doi:10.1021/acs.est.8b01679 | 12,600 samples; about 120 million (9%) at risk | doi.org and Crossref OK, abstract read |
| S51 | UNICEF, Advancing Climate Resilient WASH Services (2019–2024 progress) | UN agency document | https://www.unicef.org/executiveboard/media/33586/file/Advancing-Climate-Resilient-WASH-Services.pdf | Definition criteria; 34.3m; 19.9m; 11,019 solar systems; 92 countries | PDF fetched, figures found |
| S52 | UNICEF, Guidance Note: climate resilient WASH | UN agency web page | https://www.unicef.org/documents/unicef-guidance-note-climate-resilient-wash | Existence of programming guidance | Fetched |
| S53 | de Sousa Filho et al. (2025), Water, Health, and Social Technologies: One Million Cisterns Programme Case Study, Annals of Global Health 91(1) | Peer-reviewed | doi:10.5334/aogh.4754; https://annalsofglobalhealth.org/articles/10.5334/aogh.4754 | 1,146,210 cisterns by 2022; over 100,000 a year in 2013–15; 5,946 in 2022; eight studies; 73% fewer diarrhoea episodes (RR 0.27) | doi.org and Crossref OK; full text fetched |
| S54 | World Bank (2006), Urban Water Sector Reform in Senegal: Innovative contract design to expand services to the poor (feature no. 7) | Multilateral note | https://documents1.worldbank.org/curated/en/319211468103464104/pdf/383170senegal0wss0feature0no7.pdf | SONES/SDE model; 10-year affermage; UFW 31.5% to 20%; 81,000 social connections | PDF fetched, figures found |
| S55 | World Bank (2022), Implementation Completion and Results Report ICR00005755, Senegal Urban Water and Sanitation Project | Multilateral report | https://documents1.worldbank.org/curated/en/948231655388003968/pdf/Senegal-Urban-Water-and-Sanitation-Project.pdf | Second-generation reform; re-bidding; SEN'EAU as new operator | PDF fetched, text found |
| S56 | Press Information Bureau, Govt of India, 14 Aug 2026, "Jal Jeevan Mission Clocks Seven Years of Rural Transformation" | Government press release | https://www.pib.gov.in/PressReleasePage.aspx?PRID=2299487&lang=2&reg=48&v=sdd | 3.23 crore (16.72%) to 15.91 crore (82.24%); extension to Dec 2028; JJM 2.0 | Fetched, figures found |
| S57 | Water Point Data Exchange (WPDx), home page | NGO platform | https://www.waterpointdata.org/ | Purpose; data standard; Global Water Challenge | Fetched (web-fetch) |
| S58 | WPDx data portal | NGO data platform | https://data.waterpointdata.org/ | Open access to the full dataset | Fetched |

- **S59** United Nations, Sustainable Development Goal 6, official targets and indicators. Target 6.3 (water quality and wastewater), 6.4 (water-use efficiency and scarcity), 6.5 (integrated water resources management), 6.6 (water-related ecosystems), 6.a (international cooperation) and 6.b ("Support and strengthen the participation of local communities in improving water and sanitation management"). https://sdgs.un.org/goals/goal6 (page opened 18 November 2026).

## Checked but not cited

| Source | Identifier | Reason not cited |
| --- | --- | --- |
| Hutton & Varughese (2016), summary version | doi:10.1596/k8632 | doi.org and Crossref OK; the full report (S4) is cited instead |
| Schiffler (2015), Cochabamba chapter in Water, Politics and Money | doi:10.1007/978-3-319-16691-9_2 | doi.org and Crossref OK, but no abstract or text read |
| Transnational Institute, Our Public Water Future (2015) | https://www.tni.org/en/publication/our-public-water-future | Returned HTTP 429 to both fetch methods; remunicipalisation counts taken via S45 instead |
| UNICEF data page for JMP 2025 | https://data.unicef.org/resources/jmp-report-2025/ | HTTP 403; figures taken from the report itself (S1) |
| Jal Jeevan Mission dashboard (state-wise tap connections) | https://ejalshakti.gov.in/JJM/JJMReports/Physical/JJMRpt_StateWiseTapConnection.aspx | No response; live dashboard figure not verified, so the PIB release (S56) is used |
| AFD technical note on Senegal water | https://www.afd.fr/sites/default/files/2018-03-07-49-41/41-VA-notes-techniques.pdf | HTTP 403 |
| FundiFix model paper (Loughborough repository) | https://dspace.lboro.ac.uk/2134/31331 | HTTP 403; S27–S29 cover the evidence |
| UN Mandate Registry entry for A/HRC/RES/15/9 | https://mandates.un.org/mandate/A/HRC/RES/15/9 | Fetched; the resolution itself (S33) is cited |
| British Geological Survey NORA record for the 2001 arsenic report | http://nora.nerc.ac.uk/11986 | HTTP 500; its exposure estimate (35 million above 50 µg/L) is therefore not used |

## Revisions (18 November 2026, round 7/8 audit)

- Corrected the method section so that labels refer to README.md, not CASE-STUDY.md.
- Added S59, the official SDG 6 target list, including target 6.b.

