<div class='flex h-screen m-auto w-screen justify-center items-center'>
    <div class='h-full w-full flex flex-col'>
        <div class="bg-red-100 text-black p-5 text-4xl flex justify-center items-center">
            Dashboard
                <button class="absolute left-5  text-3xl" >&lt;<a class="hover:underline decoration-2" href="/appointment">Appointment</a></button>
            
        </div>
        <div class="h-full text-4xl w-full">
            <!--Here i will make some data vis-->

            <div class="flex flex-col bg-red-300 h-full w-full justify-start items-start gap-5 p-5">
                
                <div id="time-series-plot" class="bg-red-100 m-0 w-full flex justify-center items-center">
                </div>

                <div class='flex flex-row w-full justify-between gap-5'>

                    <div id="appointment-type" class="bg-red-100 m-0 w-full flex justify-center items-center">
                    </div>
                    <div id="repair-type" class="bg-red-100 m-0 w-full flex justify-center items-center">
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    import { onMount } from "svelte";
    import * as d3 from "d3";

    
    //we need to make some random data as an example
    //the data will be in json format, one object for each recorded appointment
    //get the data from file MOCK_DATA.json in src/lib
    let data = []

    function repairType(data){
        //make a pie chart of the data
        const repairTypeCounts = d3.rollup(data, v => v.length, d => d.repair);
        const repairTypeCountsArray = Array.from(repairTypeCounts, ([key, value]) => ({key, value}));
        console.log(repairTypeCountsArray);

        const margin = {top: 30, right: 30, bottom: 30, left: 30};
        const width = document.getElementById("repair-type").clientWidth - margin.left - margin.right;
        const height = 400 - margin.top - margin.bottom;

        const svg = d3.select("#repair-type")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            svg.attr("transform", `translate(${margin.left + width / 2}, ${margin.top + height / 2})`);

        const color = d3.scaleOrdinal()
            .domain(repairTypeCountsArray.map(d => d.key))
            .range(d3.schemeSet2);

        const pie = d3.pie()
            .value(d => d.value);


        const data_ready = pie(repairTypeCountsArray);
        console.log(data_ready);

        const arc = d3.arc()
            .innerRadius(0)
            .outerRadius(Math.min(width, height) / 2 - 1);

        svg.selectAll("path")
            .data(data_ready)
            .enter()
            .append("path")
            .attr("d", arc)
            .attr("fill", d => color(d.data.key))

            .style("opacity", 0.7)
            .attr("transform", `translate(${width / 5}, 0)`)
            .on("mouseover", mouseover)
            .on("mousemove", mousemove)
            .on("mouseleave", mouseleave);


        // Tooltip setup
        const tooltip = d3.select("#repair-type")
            .append("div")
            .style("opacity", 0)
            .attr("class", "tooltip") // Ensure this class is defined in your CSS
            .style("background-color", "white")
            .style("border", "solid")
            .style("border-width", "2px")
            .style("border-radius", "5px")
            .style("padding", "5px")
            .style("position", "absolute");

        // Mouseover event
        function mouseover(event, d) {
            tooltip.style("opacity", 1);
            d3.select(this)
                .style("stroke", "black")
                .style("opacity", 1);
        }

        // Mousemove event
        function mousemove(event, d) {
            tooltip.html("Type: " + d.data.key + "<br>Count: " + d.data.value)
                .style("left", (event.pageX + 10) + "px")
                .style("top", (event.pageY + 10) + "px");
        }

        // Mouseleave event
        function mouseleave(event, d) {
            tooltip.style("opacity", 0);
            d3.select(this)
                .style("stroke", "none")
                .style("opacity", 0.7);
        }

        //add a title
        svg.append("text")
            .attr("x", -width/2 + margin.left + 150)
            .attr("y", -height/2 + margin.top )
            .attr("text-anchor", "middle")
            
            .style("font-size", "24px")
            .text("Repair type distribution");

        //add a legend
        const legend = svg.selectAll(".legend")
            .data(repairTypeCountsArray)
            .enter()
            .append("g")
            .attr("class", "legend")
            //move the legend to the right
            .attr("transform", (d, i) => `translate(${margin.left + 80}, ${i * 30})`);

        legend.append("rect")
            .attr("x", -width/2)
            .attr("width", 18)
            .attr("height", 18)
            //add gap between legend items
            
            .style("fill", d => color(d.key))

        legend.append("text")
            .attr("x", -width/2 - 6)
            .attr("y", 9)
            //smaller font
            .attr("dy", ".35em")
            .style("text-anchor", "end")
            .style("font-size", "18px")
            .text(d => d.key);

    }

    function appointmentType(data){
        //make a pie chart of the data
        const appointmentTypeCounts = d3.rollup(data, v => v.length, d => d.type);
        const appointmentTypeCountsArray = Array.from(appointmentTypeCounts, ([key, value]) => ({key, value}));
        console.log(appointmentTypeCountsArray);

        const margin = {top: 30, right: 30, bottom: 30, left: 30};
        const width = document.getElementById("appointment-type").clientWidth - margin.left - margin.right;
        const height = 400 - margin.top - margin.bottom;

        const svg = d3.select("#appointment-type")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            svg.attr("transform", `translate(${margin.left + width / 2}, ${margin.top + height / 2})`);

        const color = d3.scaleOrdinal()
            .domain(appointmentTypeCountsArray.map(d => d.key))
            .range(d3.schemeSet2);

        const pie = d3.pie()
            .value(d => d.value);


        const data_ready = pie(appointmentTypeCountsArray);
        console.log(data_ready);
        

        const arc = d3.arc()
            .innerRadius(0)
            .outerRadius(Math.min(width, height) / 2 - 1);

        svg.selectAll("path")
            .data(data_ready)
            .enter()
            .append("path")
            .attr("d", arc)
            .attr("fill", d => color(d.data.key))
            
            .style("opacity", 0.7)
            .attr("transform", `translate(${width / 5}, 0)`)
            .on("mouseover", mouseover)
            .on("mousemove", mousemove)
            .on("mouseleave", mouseleave);

        // Tooltip setup
        const tooltip = d3.select("#appointment-type")
            .append("div")
            .style("opacity", 0)
            .attr("class", "tooltip") // Ensure this class is defined in your CSS
            .style("background-color", "white")
            .style("border", "solid")
            .style("border-width", "2px")
            .style("border-radius", "5px")
            .style("padding", "5px")
            .style("position", "absolute");

        // Mouseover event
        function mouseover(event, d) {
            tooltip.style("opacity", 1);
            d3.select(this)
                .style("stroke", "black")
                .style("opacity", 1);
        }

        // Mousemove event
        function mousemove(event, d) {
            tooltip.html("Type: " + d.data.key + "<br>Count: " + d.data.value)
                .style("left", (event.pageX + 10) + "px")
                .style("top", (event.pageY + 10) + "px");
        }

        // Mouseleave event
        function mouseleave(event, d) {
            tooltip.style("opacity", 0);
            d3.select(this)
                .style("stroke", "none")
                .style("opacity", 0.7);
        }

        //add a title
        svg.append("text")
            .attr("x", -width/2 + margin.left + 150)
            .attr("y", -height/2 + margin.top )
            .attr("text-anchor", "middle")
            
            .style("font-size", "24px")
            .text("Appointment type distribution");

        //add a legend
        const legend = svg.selectAll(".legend")
            .data(appointmentTypeCountsArray)
            .enter()
            .append("g")
            .attr("class", "legend")
            //move the legend to the right
            .attr("transform", (d, i) => `translate(${margin.left + 80}, ${i * 30})`);

        legend.append("rect")
            .attr("x", -width/2)
            .attr("width", 18)
            .attr("height", 18)
            //add gap between legend items
            
            .style("fill", d => color(d.key))
        


        legend.append("text")
            .attr("x", -width/2 - 6)
            .attr("y", 9)
            //smaller font
            .attr("dy", ".35em")
            .style("text-anchor", "end")
            .style("font-size", "18px")
            .text(d => d.key);



    }

    function timeSeriesPlot(data){
        //make a time series plot of the data (first need to isolate the date (no time) 
        data.forEach(d => {
            d.datetime = new Date(d.datetime);
            d.date = new Date(d.datetime.getFullYear(), d.datetime.getMonth(), d.datetime.getDate());
        });

        const repairCountsByDate = d3.rollup(data, v => v.length, d => d.date);
        const repairCountsByDateArray = Array.from(repairCountsByDate, ([key, value]) => ({key, value}));
        repairCountsByDateArray.sort((a, b) => d3.ascending(a.key, b.key));
        console.log(repairCountsByDateArray);

        const margin = {top: 80, right: 80, bottom: 80, left: 80};
        const width = document.getElementById("time-series-plot").clientWidth - margin.left - margin.right -500;
        const height = 400 - margin.top - margin.bottom;

        const svg = d3.select("#time-series-plot")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            .attr("transform", `translate(${margin.left}, ${margin.top})`);

        const x = d3.scaleBand()
            //put the labels vertically, and only every 5th label
            .domain(repairCountsByDateArray.map(d => d.key))
            .range([0, width])
            .padding(0.1);
        svg.append("g")
            .attr("transform", `translate(0, ${height})`)
            .call(d3.axisBottom(x))
            .selectAll("text")
            //take only the first 5 characters of the date
            .text(d => d.toISOString().slice(0, 10))
            .attr("transform", "translate(-10,0)rotate(-45)")
            .style("text-anchor", "end");

        

        const y = d3.scaleLinear()
            .domain([0, d3.max(repairCountsByDateArray, d => d.value)])
            .range([height, 0]);
        svg.append("g")
            .call(d3.axisLeft(y));

        svg.selectAll("bar")
            .data(repairCountsByDateArray)
            .enter()
            .append("rect")
            .attr("x", d => x(d.key))
            .attr("y", d => y(d.value))
            .attr("width", x.bandwidth())
            .attr("height", d => height - y(d.value))
            .attr("fill", "steelblue")
            .style("opacity", 0.8)

            .on("mouseover", mouseover)
            .on("mousemove", mousemove)
            .on("mouseleave", mouseleave);
            

            // Tooltip setup
    const tooltip = d3.select("#time-series-plot")
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip") // Make sure to define this class in your CSS
        .style("background-color", "white")
        .style("border", "solid")
        .style("border-width", "2px")
        .style("border-radius", "5px")
        .style("padding", "5px")
        .style("position", "absolute");

    // Mouseover event
    function mouseover(event, d) {
        tooltip.style("opacity", 1);
        d3.select(this)
            .style("stroke", "black")
            .style("opacity", 1);
    }

    // Mousemove event
    function mousemove(event, d) {
        tooltip.html("Date: " + d.key.toISOString().slice(0, 10) + "<br>Count: " + d.value)
            .style("left", (event.pageX + 10) + "px")
            .style("top", (event.pageY + 10) + "px");
    }

    // Mouseleave event
    function mouseleave(event, d) {
        tooltip.style("opacity", 0);
        d3.select(this)
            .style("stroke", "none")
            .style("opacity", 0.8);
    }


        //add a title
        svg.append("text")
            .attr("x", (width / 2))
            .attr("y", 0 - (margin.top / 2))
            .attr("text-anchor", "middle")
            .style("font-size", "24px")
            .text("Number of repairs by date");

    }


    onMount(async () => {
        const res = await fetch("MOCK_DATA.json");
        const data = await res.json();
        console.log(data);
        timeSeriesPlot(data);
        appointmentType(data);
        repairType(data);


        
    });
    
    
</script>
                