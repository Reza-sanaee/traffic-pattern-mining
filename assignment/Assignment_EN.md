# Road-camera analytics: complete four-part assignment

## Scenario and independent deliverables

Act as a data-analysis engineer for a road authority. Use vehicle-passage records to investigate registration quality, traffic patterns, clustering, streaming, graphs, and anomalies. Minimum implementation does not guarantee the top relative grade: justify the problem, assumptions, parameters, quality checks, visualization, and decision relevance. For each of the four parts provide runnable documented code, configurable parameters, meaningful visualization, a separate engineering report, and a presentation video no longer than ten minutes. Thus the course requires four independent reports and four videos. Each report explains problem/assumptions, data/preprocessing, model/parameter design, results/insights, validation and sensitivity, limitations, and extensions. README documents execution, inputs/outputs, dependencies, and key parameters. A video explains purpose, methods, functions or endpoints, interpretation, and limitations. The supplied files determine which deliverables were actually archived; do not infer missing videos from these requirements.

## Data dictionary and identifier interpretation

CompanyCode denotes operator/contractor; DeviceID identifies a camera; Lane identifies its lane; PassedTime is the passage timestamp; ReceivedTime is system receipt; event_ts uses parsed PassedTime and otherwise the declared receipt-time fallback. PlateType and VehicleType are category codes; Speed is recorded speed; road_code/road_name identify the road; state_name/state_code identify the province. StreetID, PassedVehicleRecordsId, and SystemCode are internal/system identifiers. The assignment describes nine-digit encoded plate values with a two-digit letter code. Other digits have been consistently indexed for privacy. Treat them as pseudonymous identifiers, not recoverable raw registration numbers. Code 21 represents the letter Ain, suggested as a heavy-fleet proxy. A proxy is not guaranteed ground-truth vehicle classification. Keep identifiers as strings when leading zeros matter. 

## Part 1: frequent patterns, registration-quality heuristics, and LSH

Load and preprocess the data and optimize access with a B-tree-based index as requested. Consider (PlateValue,event_ts), DeviceID, or a justified alternative. Measure at least one relevant operation before/after indexing, such as basket construction, route preparation, or exact Jaccard computation. Explain when the index helps and when it does not. A columnar file layout can be an alternative experiment but is not literally the requested B-tree. Define BasketID=(PlateValue,Window(event_ts,delta_t)). Try a short interval such as 30 minutes and one hour; justify the choice and examine sensitivity. Define items as (CompanyCode,DeviceID), or compare a stated alternative. Deduplicate items within a set-based basket. Use Apriori or PCY as specified and extract/interpret frequent triples with a justified minimum support. A triple is co-occurrence, not an automatically established ordered route. Use frequent patterns to identify potentially missing registrations. State how an expected observation and denominator are formed, compute camera-level miss rates, aggregate to company level, and compare traffic volume with rate. Consider speed bands, lane, and time effects. Explain possible alternate routes and incomplete observation before declaring hardware failure. For each vehicle extract a set of road codes, generate MinHash signatures, use LSH banding to propose similar pairs, and compute exact Jaccard for candidates. Report signature/band parameters, sample restrictions, and thresholds. Compare miss heuristics inside similar-route groups with the overall rate. Sets do not preserve traversal order, travel time, or lane; matching them does not prove an identical physical trip. Deeper analysis and parameter sensitivity form part of evaluation.

## Part 2: five entity-clustering scenarios

1. Camera behavior: aggregate by DeviceID, including 24-hour temporal profile, speed mean/dispersion, heavy ratio, and weekly stability. Normalize to avoid scale domination, use PCA, compare K-Means initializations, and interpret roles such as transit/night, morning commute, or uniform traffic.
2. Lane behavior: use (DeviceID,Lane), with temporal profile, speed, fleet composition, and lane share of camera traffic. Normalize, reduce dimension, cluster, and explain within-camera differences.
3. Road behavior: aggregate all cameras on road_code or road_name. Include time distribution, speed, fleet composition, and plate diversity. Interpret road groups and discuss policy relevance without claiming that clustering alone selects an optimal intervention.
4. Vehicle behavior: use hashed/pseudonymous MasterPlateValue, require sufficient records, and describe usual hours, road diversity, speed, and vehicle-type proxy. The limited one-week horizon and large identity count require careful sampling and noise thresholds.
5. Camera data quality: aggregate receipt-minus-passage delay, invalid PlateType, missing fields, and temporal instability. Group devices by observed quality profiles and propose investigation priorities. Validate any diagnosis against external maintenance evidence.

## Part 3: streaming architecture and API presentation

Select the busiest day by PassedTime/event date. Extract its records, order by ReceivedTime, and replay them line by line through a socket producer. Make rate configurable and compare several rates, with the assignment suggesting examples around 50, 100, and 1000 records per second. Messages include DeviceID, PlateValue, event_ts, ReceivedTime, Speed, road_code, and state_name. Preserve original timestamps independently of replay wall time. Choose a window duration, such as one or five minutes, and a slide. Provide structured API endpoints for every implemented analysis and a separate visualization program that calls them. FastAPI is suggested but not mandatory. Document endpoint fields, units, freshness, and errors. Compute total and per-camera counts, and the three busiest cameras per window. Explain the need for aggregation, the sensitivity/stability tradeoff, and short/long-window effects. Analyze receipt-minus-event delay with mean, maximum, and distribution; compare event-time and receipt-time windows and late-data handling. Compare at least two window lengths. Examine camera shares, unusually dominant cameras, and rank stability. Compute average speed and study its relationship to counts. Define any congestion indicator explicitly and discuss its limits. Identify busiest roads and provincial patterns/hotspots. Keep geographic aggregation distinct from a validated road-density measurement. Maintain previous-window state and cumulative counts since stream start. Ensure overlapping-window records are counted only once in true cumulative totals; repeated window updates need stable identities or equivalent idempotent logic. Maintain an online EWMA mean/variation baseline per camera. Calculate a drop anomaly score and require L consecutive abnormal windows before alarming. Return camera, window, observed count, baseline mean/variation, score, and alarm status. Include zero-count windows for monitored inventory so fully silent cameras are visible. Design a health index from stability, fluctuations, and anomalies with justified thresholds for healthy/suspicious/critical. Forecast the next window from past windows, compare prediction and actual count, and report quantitative error across a sufficient sequence. A forecast or alert is not a validated diagnosis merely because the dashboard displays it.

## Part 4: movement graphs, PageRank, HITS, and comparison

Sort each vehicle's observations and form consecutive transitions. Build directed camera, road, and province graphs, with optional additional models. Report node/edge counts, in/out degree or weighted strength, and prominent links. Define structural anomalies precisely, such as changes in common links, unusual travel times, or incomplete next observations. Select meaningful subgraphs and visualize them. Run PageRank on all three graphs, explain what weighted structural importance means at each level, incorporate rank into explicitly defined anomaly criteria, and visualize node/link importance. Specify damping, teleportation, weights, dangling nodes, convergence, and any custom link-importance formula. Run HITS on the camera graph and interpret hub/authority roles. Define a bipartite model such as vehicle-to-camera, analyze its HITS output, and compare the information it provides with consecutive-camera movement. Define role-based anomalies and visualize ranks/roles. Compare structural, rank-based, and role-based anomaly results and their limitations. Missing observations, routes, sampling, one-way infrastructure, and timing gaps can all produce apparent asymmetry.

## Complete operator-code reference

Operator names are rendered in English transliteration. Contract descriptors distinguish existing/new and provincial/headquarters entries; identifiers are unchanged.

| CompanyCode | Operator | Contract descriptor |
|---|---|---|
| 118 | Rahkam Communications | Upgraded existing; provincial |
| 116 | Hooshmand Dayegan | Existing; provincial |
| 183 | Roshan Tolou Shargh | Existing; provincial |
| 102 | Arg | Existing; provincial |
| 100 | Pooyesh Pardaz | Existing; provincial |
| 105 | Negaresh Rayaneh | Existing; provincial |
| 205 | Fard Iran | Provincial |
| 117 | Rahkam Communications | Upgraded existing; provincial |
| 123 | Basir Pardazesh | New; headquarters |
| 121 | Tozin Electric | Provincial |
| 206 | Fanap | New; provincial |
| 313 | Paya Ravesh Malagar | New; provincial |
| 181 | Fard Iran | Headquarters |
| 122 | Negaresh Rayaneh | New; headquarters |
| 133 | Arg | Not specified |
| 131 | Ariana | New; headquarters |
| 129 | Sairan | New; headquarters |
| 184 | Badi Isfahan Intelligent Systems (Bistco) | Existing; provincial |
| 207 | Farasoo Tozin | Provincial |
| 136 | Hamsoo | New; headquarters |
| 112 | Fanap | Existing; provincial |
| 141 | Fanap Infrastructure | New; headquarters |
| 140 | Rahkam Iranian Communications | New; headquarters |
| 115 | Pand Industries | Not specified |
| 110 | Taba Tozin | Not specified |
| 168 | Isiran | New; headquarters |
| 169 | Aria Hamrah | New; headquarters |
| 173 | Basir Pardazesh | New; provincial |
| 172 | Middle East Alpha Processing | Not specified |
| 177 | Gostaran Sanaye Novin Pars | Provincial |
| 178 | Sairan | New; provincial |
| 176 | Arg | New; provincial |
| 182 | Asr Rayaneh Parseh Iranian | New; provincial |
| 174 | Roshan Tolou Shargh | New; provincial |
| 208 | Badi Isfahan Intelligent Systems (Bistco) | New; provincial |
| 314 | Sepehr Sharif Intelligent Systems | Not specified |
| 220 | Delta Pars | New; provincial |

## Complete plate-letter code reference

The 34 codes below retain their two-digit identifiers. English letter names describe the encoded symbols; D and S are literal Latin-letter categories. The remaining plate digits are pseudonymous indexes and do not recover an actual registration number.

| Letter code | English symbol name |
|---|---|
| 01 | Alef |
| 02 | Beh |
| 03 | Peh |
| 04 | Teh |
| 05 | Theh |
| 06 | Jim |
| 07 | Cheh |
| 08 | Heh (Arabic) |
| 09 | Kheh |
| 10 | Dal |
| 11 | Zal |
| 12 | Reh |
| 13 | Zeh |
| 14 | Zheh |
| 15 | Sin |
| 16 | Shin |
| 17 | Sad |
| 18 | Dad |
| 19 | Ta |
| 20 | Za |
| 21 | Ain |
| 22 | Ghain |
| 23 | Feh |
| 24 | Qaf |
| 25 | Kaf |
| 26 | Gaf |
| 27 | Lam |
| 28 | Mim |
| 29 | Nun |
| 30 | Vav |
| 31 | Heh |
| 32 | Yeh |
| 33 | D |
| 34 | S |
