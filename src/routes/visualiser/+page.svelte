<script>
  import {
    getEasinessChange,
    getTimeToNextReview,
    getNewEasiness,
  } from "../../modules/spacedRep";
  const grades = [0, 0.5, 1, 1.5, 2];
  const easinessChanges = grades.map((grade) =>
    getEasinessChange(grade)
  );
  const defaultEasiness = 1.15;
  let data = [];

  function handleChange(grade) {
    const change = getEasinessChange(grade);
    if (!data.length) {
      data = [
        ...data,
        {
          e: defaultEasiness, // initial e before change
          change,
          time: getTimeToNextReview(defaultEasiness + change),
        },
      ];
    } else {
      data = [
        ...data,
        {
          // e: data[data.length - 1].e + data[data.length - 1].change,
          e: getNewEasiness(data[data.length - 1].e, grade),
          change,
          time: getTimeToNextReview(
            getNewEasiness(data[data.length - 1].e, grade)
          ),
        },
      ];
    }
    console.log(data);
  }

  function formatTimePeriod(minutes) {
    minutes = Math.round(minutes);
    if (isNaN(minutes) || minutes <= 0) {
      return "0 m";
    }

    const days = Math.floor(minutes / 1440); // 60 minutes * 24 hours
    const hours = Math.floor((minutes % 1440) / 60);
    const remainingMinutes = minutes % 60;

    let formattedTime = "";

    if (days > 0) {
      formattedTime += `${days}d `;
    }

    if (hours > 0) {
      formattedTime += `${hours}h `;
    }

    if (remainingMinutes > 0) {
      formattedTime += `${remainingMinutes}m`;
    }

    return formattedTime.trim();
  }
</script>

<section>
  <div class="data">
    {#each data as dataChunk}
      <div class="line-segment">
        <div class="e">
          {dataChunk.e.toPrecision(7).replace(/\.?0+$/, "")}
        </div>
        <div class="change">
          {dataChunk.change >= 0 ? "+" : ""}{dataChunk.change}
        </div>
        <div class="time">{formatTimePeriod(dataChunk.time)}</div>
      </div>
    {/each}
  </div>
  <div class="grade-buttons">
    {#each grades as grade, i}
      <button on:click={() => handleChange(grade)}
        >{easinessChanges[i] >= 0 ? "+" : ""}{easinessChanges[
          i
        ]}</button
      >
    {/each}
  </div>
  <button class="primary reset" on:click={() => (data = [])}
    >reset</button
  >
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  button.reset {
    margin-inline: auto;
  }
  .grade-buttons {
    display: flex;
    gap: 1rem;
    justify-content: center;
  }
  .grade-buttons button {
    border: 1px solid var(--border-dark);
    padding: 1rem;
    background-color: var(--background-alt);
  }
  .data {
    padding: 5rem 0;
    width: 100%;
    display: flex;
    font-size: 0.5rem;
  }
  .line-segment {
    position: relative;
    height: 3px;
    background-color: var(--text);
    width: 100%;
  }
  .line-segment > div {
    position: absolute;
    transform: translateX(-50%);
  }
  .line-segment .e {
    top: 0.25rem;
  }
  .line-segment .time {
    left: 50%;
    bottom: 0.25rem;
  }
  .line-segment .change {
    left: 50%;
    top: 0.25rem;
  }
  .line-segment::after {
    content: "";
    display: block;
    width: 0.5rem;
    height: 0.5rem;
    background-color: var(--primary);
    border-radius: 50%;
    right: -0.15rem;
    top: -0.15rem;
    position: absolute;
    z-index: 1;
  }
</style>
