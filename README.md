```Javascript
//index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <!-- heading
        user-container
        stats-container -->

        <h1>LeetMatric</h1>

        <div class="user-container">
            <p>Enter you username below:</p>
            <div class="user-input-container">
                <input type="text" placeholder="Enter your username" id="user-input">
                <button id="search-button">Search</button>
            </div>
        </div>

        <div class="stats-container">
            <div class="progress">
                <div class="progress-item">
                    <div class="easy-progress circle">
                        <span id="easy-lable"></span>
                        Easy
                    </div>
                </div>
                <div class="progress-item"></div>
                    <div class="medium-progress circle">
                        <span id="medium-lable"></span>
                        medium
                    </div>
                <div class="progress-item"></div>
                    <div class="hard-progress circle">
                        <span id="hard-lable"></span>
                         Hard
                    </div>
            </div>
            
            <div class="stats-card">
                <!-- data entered using javascript -->
            </div>

        </div>

    </div>

    <script src="index.js"></script>
</body>
</html>



//index.css

*{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body{
    /* background-color: rgb(25, 40, 68); */
    background-color: black;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

h1{
    
    color:#8454bc;
    font-weight: bolder;
    font-family:fantasy;
}
.container{
    display:flex;
    flex-direction: column;
    gap: 2rem;
    /* background-color: rgb(36, 64, 85); */
    background-color: rgb(34, 32, 32);
    color: white;
    padding: 20px;
    border-radius: 5px;
    width: 50%;
    /* height: 50%; */
    /* max-width: 600px; */
}

.user-container{
    display: flex;
    flex-direction: column;
    gap: 10px;
    justify-content: start;
}

.user-input-container{
    display: flex;
    justify-content: space-between;
}

#user-input{
    width: 85%;
    padding: 0.3rem;
    border-radius: 5px;
}

#search-button{
    width: 11%;
    padding: 0.3rem;
    border-radius: 5px;
}

.circle{
    width: 150px;
    height: 150px;
    border-radius: 50%;
    border: 4px solid #6428a4;
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 16px;
    /* color: white; */
    font-weight: 700;
    background: conic-gradient(#6428a4 var(--progress-degree,0%),#2f283a 0%);
}
.circle span{
    position: relative;
    z-index: 2;
}

.progress{
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
    flex-wrap: wrap;
    gap: 20px;
}

.stats-card{
    margin-top: 2rem;
    margin-bottom: 2rem;
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
    gap: 10px;
}

h4{
    font-size: 1rem;
}

.card{
    background-color: #2f283a;
    width: 40%;
    max-width: 290px;
    min-height: 4rem;
    padding: 20px;
    border-radius: 10px;
    font-size: 1rem;
    /* color: black; */

}



//index.js

const easyProgressCircle = document.querySelector(".easy-progress");
const mediumProgressCircle = document.querySelector(".medium-progress");
const hardProgressCircle = document.querySelector(".hard-progress");

let lbl1 = document.querySelector("#easy-lable");
let lbl2 = document.querySelector("#medium-lable");
let lbl3 = document.querySelector("#hard-lable");

document.addEventListener("DOMContentLoaded", function() {

    const searchBtn = document.getElementById("search-button");
    const usernameInput = document.getElementById("user-input");
    const statsContainer = document.querySelector(".stats-container");
    const cardStatsContainer = document.querySelector(".stats-card");

    function validateUsername(username){
        if(username.trim() === ""){
            alert("Username should not be Empty");
            return false;
        }
        const regex = /^[a-zA-Z0-9_-]{1,15}$/;
        const isMatching = regex.test(username);
        if(!isMatching){
            alert("Invalid Username");
        }
        return isMatching;
    }

    async function fetchUserDetails(username){
        
        try{
            searchBtn.textContent = "searching..";
            searchBtn.disabled = true;
        const proxyUrl = 'https://cors-anywhere.herokuapp.com/'
        const targetUrl = 'https://leetcode.com/graphql/';

        const myHeaders =  new Headers();
        myHeaders.append("content-type", "application/json");

        const graphql = JSON.stringify({
          query:
            "\n query userSessionProgress($username: String!) {\n allQuestionsCount {\n  difficulty\n  count\n  }\n  matchedUser(username: $username) {\n submitStats {\n   acSubmissionNum {\n        difficulty\n    count\n      submissions\n       }\n      totalSubmissionNum {\n    difficulty\n        count\n      submissions\n     }\n    }\n }\n}\n   ",
            variables: {"username": `${username}` }
        })

        const requestOptions = {
            method: "POST",
            headers: myHeaders,
            body: graphql,
            redirect: "follow"
        };

        const response = await fetch(proxyUrl+targetUrl, requestOptions);
        
            if(!response.ok){
                throw new Error("Unable to fetch User details");
            }

            const parsedData = await response.json();
            console.log("login data", parsedData);

            displayUserData(parsedData);
        }
        catch(error){
            console.log(error);
            statsContainer.innerHTML = `<p> No Data Found </p>`
        }
        finally{
            searchBtn.textContent = "search";
            searchBtn.disabled = false
        }
     }

    function updateProgress(solved, total, label, circle){
        const progressDegree = (solved/total)*100;
        circle.style.setProperty("--progress-degree", `${progressDegree}%`);
        label.textContent = `${solved}/${total}`;
    }
    
    function displayUserData(parsedData){
        const totalQues = parsedData.data.allQuestionsCount[0].count;
        const totalEasyQues = parsedData.data.allQuestionsCount[1].count;
        const totalMediumQues = parsedData.data.allQuestionsCount[2].count;
        const totalHardQues = parsedData.data.allQuestionsCount[3].count;

        const solvedTotalQues = parsedData.data.matchedUser.submitStats.acSubmissionNum[0].count;
        const solvedTotalEasyQues = parsedData.data.matchedUser.submitStats.acSubmissionNum[1].count;
        const solvedTotalMediumQues = parsedData.data.matchedUser.submitStats.acSubmissionNum[2].count;
        const solvedTotalHardQues = parsedData.data.matchedUser.submitStats.acSubmissionNum[3].count;

        updateProgress(solvedTotalEasyQues, totalEasyQues, lbl1, easyProgressCircle);
        updateProgress(solvedTotalMediumQues, totalMediumQues, lbl2, mediumProgressCircle);
        updateProgress(solvedTotalHardQues, totalHardQues, lbl3, hardProgressCircle);

        const cardData = [
            {label: "Overall Submissions", value:parsedData
            .data.matchedUser.submitStats.totalSubmissionNum[0].submissions},
            {label:"Overall Easy Submissions", value:parsedData
            .data.matchedUser.submitStats.totalSubmissionNum[1].submissions},
            {label:"Overall Medium Submissions", value:parsedData
            .data.matchedUser.submitStats.totalSubmissionNum[2].submissions},
            {label:"Overall  Hard Submissions", value:parsedData
            .data.matchedUser.submitStats.totalSubmissionNum[3].submissions},
        ];

        console.log("card ka data: ", cardData);

        cardStatsContainer.innerHTML = cardData.map(
            data => 
                `
                    <div class="card">
                    <h4>${data.label}</h4>
                    <p>${data.value}</p>
                    </div>
                `
        ).join("")
    }

    searchBtn.addEventListener('click', function(){
        const username = usernameInput.value;
        console.log("User ka Name", username);
        if(validateUsername(username)) {
            fetchUserDetails(username);
        }
    })
})  
```
