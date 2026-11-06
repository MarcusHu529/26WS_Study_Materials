## What is Data Visualization?
**Definition:** Data visualization transforms data into visual representations to help people understand information, identify patterns, make comparisons, and make decisions.
**Two Main Disciplines:**
- Scientific Visualization (SciVis)
	- Works with physical or spatial data, such as temperature fields, airflow, and medical scans. Spatial structure is usually inherent in the data.
- Information Visuallization (InfoVis)
	- Works with abstract data, such as networks, hierachies, and business statistics. Spatial layout is chosen by the designer.
- SciVis typically visualizaes inherently spatial data
- InfoVis usually gives abstract data a visual layout
## Visualization Pipeline

![[DataVis Pipeline.png|526]]
## Data Models: Domain Space vs. Feature Space
**Domain (Observation Space)**: Where the measurements are taken, or which observation locations are considered.
**Feature Space**: What is measured at those locations.
**Example:**
- Measuring temperature in a 3D room.
$$(x,y,z)\in\mathbb R^3$$
- The feature is one temperature value:
$$T\in\mathbb R$$
- So the data can be modeled as:
$$T:\mathbb R^3\rightarrow\mathbb R$$
	- Give me a location in the room, and I will give you the temperature there.
- If we measure wind velocity instead, the feature has three components:
$$\mathbf v:\mathbb R^3\rightarrow\mathbb R^3$$
	- The location has three dimensions, and the velocity vector also has three.
- **Remember:** Domain = where; feature = what is measured. The two dimensionalities are independent concepts.
## Data Types

| Data Type    | Meaning                                     | Example                   |
| ------------ | ------------------------------------------- | ------------------------- |
| Nominal      | Categories without an inherent order        | Car brands, countries     |
| Ordinal      | Categories with an order                    | Cold, warm, hot           |
| Quantitative | Numerical values with meaningful arithmetic | Temerature, height, speed |
For example, car brands cannot meaningfully be averaged. Satisfaction ratings can be ranked but arithmetic on their labels may not be meaningful. Actual measured heights can be averaged.

| Feature Type | Plain English                                  | Example                     |
| ------------ | ---------------------------------------------- | --------------------------- |
| Scalar       | One numerical value                            | Temperature = 25 degree     |
| Vector       | Several components representing a vector       | Wind velocity = (2,3,0)     |
| Tensor       | A more general multi-index numerical structure | Stress tensor in a material |
**Remember:** Data type and feature dimensionality influence which visual representations are appropriate.
## Visualization Tasks and Interaction
- A visualization should be designed around the task the user wants to accomplish
	- **Exploration**: where we search for patterns and hypotheses
	- **Validation**: where we check hypotheses
	- **Presentation**: where we communicate results
- **Shneiderman's visual information-seeking mantra**
	- Overview first, zoom and filter, then details on demand
- The seven interaction tasks are *overview, zoom, filter, details-on-demand, relate, history, and extract.*
	- Example: In a large factory monitoring dashboard, an engineer first sees all machines, filters to those reporting high temperatures, selects one machine for details, and compares its measurements over time.
## Misleading Visualizations
[[Data Visualization - Introduction.pdf#page=25|Lecture-01, page 25]]
A visualization must preserve the meaning of the underlying quantities, not just look convincing.
## Observation Space and Grid Types

| Type             | Meaning                                                                                             |
| ---------------- | --------------------------------------------------------------------------------------------------- |
| Reference point  | A value applies only to a specific observation point                                                |
| Local reference  | A value desribes a local region; values at unmeasured locations may be estimated with interpolation |
| Global reference | A value applies to the entire observation space                                                     |
**Grid types**
- Including regular, rectilinear, curvilinear, irregular, and unstructured grids.
- Grid geometry describes where points are located; connectivity describes which points are connected. A regular grid has predictable geometry and connectivity, while an unstructured grid stores connections explicitly.