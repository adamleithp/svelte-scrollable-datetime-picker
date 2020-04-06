<script>
	import { onMount } from 'svelte';

	// Props.
	const twentyFourHourClock = false

	// Constants
	let months = 12;
	let monthDays = 31;
	const NOW = new Date()
	const monthByName = [
		'Jan',
		'Feb',
		'Mar',
		'Apr',
		'May',
		'Jun',
		'Jul',
		'Aug',
		'Sep',
		'Oct',
		'Nov',
		'Dec',
	]

	const daysOfWeekByName = [
		'Sun',
		'Mon',
		'Tue',
		'Wed',
		'Thu',
		'Fri',
		'Sat',
	]

	const currentYear = NOW.getFullYear()
	const currentMonth = NOW.getMonth()
	const currentDay = NOW.getDate()
	const currentHourTwentyFour = NOW.getHours()
	const currentHour = (currentHourTwentyFour > 12 ? currentHourTwentyFour - 12 : currentHourTwentyFour)
	const currentMinutes = NOW.getMinutes()
	const currentDayOfWeek = NOW.getDay()

	// Dynamic 
	let yearInput;
	let yearActiveIndex;

	let monthInput;
	let monthActiveIndex;

	let dayInput;
	let dayActiveIndex;

	let hourInput;
	let hourActiveIndex;

	let minuteInput;
	let minuteActiveIndex;

	let dayOfWeek;

	yearActiveIndex = String(currentYear).substring(0,3)[1]; // 202(This)
	monthActiveIndex = currentMonth;
	dayActiveIndex = currentDay;
	hourActiveIndex = currentHourTwentyFour; // Keep 24, only change visually
	minuteActiveIndex = currentMinutes;
	dayOfWeek = daysOfWeekByName[currentDayOfWeek]

	// If dates change, update date of week. (Fires if month, or day, or year changes:)
	$: if (dayActiveIndex >= 0) {
		const date = new Date(Number(currentYear + yearActiveIndex), monthActiveIndex, dayActiveIndex + 1, hourActiveIndex, minuteActiveIndex, 0);
		dayOfWeek = daysOfWeekByName[date.getDay()];
	}

	onMount(() => {
		// Scroll to default (today or prop)
		const currentYearElement = yearInput.children[yearActiveIndex]
		const currentMonthElement = monthInput.children[monthActiveIndex]
		const currentDayElement = dayInput.children[dayActiveIndex]
		const currentHourElement = hourInput.children[hourActiveIndex - 1]
		const currentMinuteElement = minuteInput.children[minuteActiveIndex - 1]

		// Js framework magic..
		setTimeout(() => {
			currentYearElement.scrollIntoView();
			currentMonthElement.scrollIntoView();
			currentDayElement.scrollIntoView();
			currentHourElement.scrollIntoView();
			currentMinuteElement.scrollIntoView();
		}, 0);
		
		// Mount Events
		yearInput.addEventListener('scroll', function(e) { // Do 'touchstart' event as-well
			let currentOffset = e.target.scrollTop / 16; // 16px body font size.
			let visibleIndex = currentOffset / 4; 
			if (visibleIndex % 1 === 0) yearActiveIndex = visibleIndex
		})
	
	// Mount Events
		monthInput.addEventListener('scroll', function(e) { // Do 'touchstart' event as-well
			let currentOffset = e.target.scrollTop / 16; // 16px body font size.
			let visibleIndex = currentOffset / 4; 
			if (visibleIndex % 1 === 0) monthActiveIndex = visibleIndex
		})

		dayInput.addEventListener('scroll', function(e) { // Do 'touchstart' event as-well
			let currentOffset = e.target.scrollTop / 16; // 16px body font size.
			let visibleIndex = currentOffset / 4; 
			if (visibleIndex % 1 === 0) dayActiveIndex = visibleIndex;
		})

		hourInput.addEventListener('scroll', function(e) { // Do 'touchstart' event as-well
			let currentOffset = e.target.scrollTop / 16; // 16px body font size.
			let visibleIndex = currentOffset / 4; 
			if (visibleIndex % 1 === 0) hourActiveIndex = visibleIndex;
		})

		minuteInput.addEventListener('scroll', function(e) { // Do 'touchstart' event as-well
			let currentOffset = e.target.scrollTop / 16; // 16px body font size.
			let visibleIndex = currentOffset / 4; 
			if (visibleIndex % 1 === 0) minuteActiveIndex = visibleIndex;
		})
	})


</script>


<!-- 
---------------------------
|  DEC 31   07:12   AM/PM |
--------------------------- 
-->


<div class="dtp">

	<div class="dtp-visual">
		<span class="dtp-visual-year">
			{Number(currentYear) + Number(yearActiveIndex)}
		</span>

		<span class="dtp-visual-month">
			{monthByName[monthActiveIndex]} {dayActiveIndex + 1} &nbsp; 
		</span>

		<span class="dtp-visual-hours">

			{#if twentyFourHourClock}
				{Number(hourActiveIndex) === 0 
						? '00' 
						: Number(hourActiveIndex)
				}:{Number(minuteActiveIndex) === 0 
						? '00' 
						: String(minuteActiveIndex).length >= 2 ? minuteActiveIndex : `0${minuteActiveIndex}`
				}
			{:else}
				{Number(hourActiveIndex) === 0 
						? '12' 
						: Number(hourActiveIndex) > 12 
							? Number(hourActiveIndex) - 12 
							: String(hourActiveIndex).length >= 2 ? hourActiveIndex : `0${hourActiveIndex}`
				}:{Number(minuteActiveIndex) === 0 
						? '00' 
						: String(minuteActiveIndex).length >= 2 ? minuteActiveIndex : `0${minuteActiveIndex}`
				}
			{/if}
		</span>
		
		<span class="dtp-visual-dow">
			{dayOfWeek}
		</span>

		<span class="dtp-visual-am-pm">
			{Number(hourActiveIndex) > 11 ? 'PM' : 'AM'}
		</span>
	</div>

	<div class="dtp-input">
		<ul class="dtp-item dtp-months" bind:this={yearInput}>
			{#each Array(20) as _, i}
				<li><div>{Number(currentYear) + i}</div></li>
			{/each}
		</ul>

		<ul class="dtp-item dtp-months" bind:this={monthInput}>
			{#each Array(months) as _, i}
				<li><div>{monthByName[i]}</div></li>
			{/each}
		</ul>
		
		<ul class="dtp-item dtp-days" bind:this={dayInput}>
			{#each Array(monthDays) as _, i}
				<li><div>{String(i + 1).length >= 2 ? i + 1 : `0${i + 1}`}</div></li>
			{/each}
		</ul>

		&nbsp; &nbsp;

		<ul class="dtp-item dtp-hours" bind:this={hourInput}>
			{#each Array(24) as _, i}
				<li><div>
					{#if twentyFourHourClock}
						{String(i).length >= 2 
							? i 
							: `0${i}`
						}
					{:else}
						{Number(i) === 0 
							? '12' 
							: Number(i) > 12 ? i - 12 : i
						}
					{/if}					
				</div></li>
			{/each}
		</ul>
		:
		<ul class="dtp-item dtp-minute" bind:this={minuteInput}>
			{#each Array(60) as _, i}
				<li><div>{String(i).length >= 2 ? i: `0${i}`}</div></li>
			{/each}
		</ul>



	</div>
</div>

<style lang="scss">
:global(html,body) {
	font-size: 16px;
}
ul {
	margin: 0;
	padding: 0;
}
.dtp {
	border: 1px solid #ccc;
	height: 400px;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	position: relative
}

// If you change this, you have to change the how we divide the height in the JS.
$selection-height: 4em;

.dtp-input {
	display: flex;
	align-items: center;
	justify-content: center;
}
.dtp-item {
	border: 1px solid #ccc;
	display: flex;
	height: $selection-height;
	flex-direction: column;
	overflow: scroll;
	scroll-snap-points-y: repeat($selection-height);
	scroll-snap-destination: 0 0;
	scroll-snap-type: y mandatory;
	scroll-snap-type: mandatory;
	list-style: none;

	/* FF */
	scrollbar-width: none; 
	/* Safari and Chrome */
	&::-webkit-scrollbar { 
			display: none;  
	}
	li {
		scroll-snap-align: start;

		div {
			height: $selection-height;
			display:flex;
			align-items: center;
			justify-content: center;
		}
	}
}

</style>