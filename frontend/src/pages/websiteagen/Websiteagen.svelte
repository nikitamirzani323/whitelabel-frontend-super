<script>
import { loop_guard } from "svelte/internal";


import Login from "../Login.svelte";


    import Home from "./Home.svelte";
   
    
    export let table_header_font = "";
	export let table_body_font = "";
    
    let token = localStorage.getItem("token");
    let akses_page = false;
    let listHome = [];
    let listPage = [];
    let record = "";
    let record_message = "";
    let totalrecord = 0;
    let perpage = 0;
    let page = 0;
    let totalpaging = 0;
    let totalrecordall = 0;

    async function initapp() {
        const res = await fetch("/api/valid", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: "Bearer " + token,
            },
            body: JSON.stringify({
                page: "WEBSITEAGEN-VIEW",
            }),
        });
        const json = await res.json();
        if (json.status === 400) {
            logout();
        } else if (json.status == 403) {
            alert(json.message);
        } else {
            akses_page = true;
            initHome();
        }
    }
    async function initHome(e) {
        const res = await fetch("/api/webagen", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: "Bearer " + token,
            },
            body: JSON.stringify({
                websiteagen_search: e,
                websiteagen_page : parseInt(page)
            }),
        });
        const json = await res.json();
        if (json.status == 200) {
            record = json.record;
            record_message = json.message;
            perpage = json.perpage;
            totalrecordall = json.totalrecord;
            if (record != null) {
                totalpaging = Math.ceil(parseInt(totalrecordall) / parseInt(perpage))
                totalrecord = totalrecordall;
                let no = 0
                if(page > 1){
                    no = parseInt(page) 
                }
                for (var i = 0; i < record.length; i++) {
                    let status_home = "BANNED"
                    let home_statuscss = ""
                    if(record[i]["websiteagen_status"] == "Y"){
                        status_home = "RUNNING"
                    }
                    if(record[i]["websiteagen_status"] == "Y"){
                        home_statuscss = "background:#FFEB3B;font-weight:bold;color:black;"
                    }else{
                        home_statuscss = "background:#E91E63;font-weight:bold;color:white;"
                    }
                    no = no + 1;
                    listHome = [
                        ...listHome,
                        {
                            home_no: no,
                            home_id: record[i]["websiteagen_id"],
                            home_name: record[i]["websiteagen_name"],
                            home_status: status_home,
                            home_statuscss: home_statuscss,
                            home_create: record[i]["websiteagen_create"],
                            home_update: record[i]["websiteagen_update"],
                        },
                    ];
                }
                listPage = [];
                for(var i=0;i<totalpaging;i++){
                    listPage = [
                        ...listPage,
                        {
                            page_id: i+1,
                            page_value: ((i+1*perpage)-perpage),
                            page_display: i+1 + " Of " + perpage*(i+1),
                        },
                    ];
                }
            }
        } else {
            logout();
        }
    }
    async function logout() {
        localStorage.clear();
        window.location.href = "/";
    }
    const handleRefreshData = (e) => {
        listHome = [];
        totalrecord = 0;
        setTimeout(function () {
            initHome();
        }, 500);
    };
    const handlePaging = (e) => {
        page = e.detail.page
        initHome("")
    };
    initapp()
</script>

{#if akses_page == true}
<Home
    on:handlePaging={handlePaging}
    on:handleRefreshData={handleRefreshData}
    {token}
    {table_header_font}
    {table_body_font}
    {listPage}
    {listHome}
    {totalrecord}/>
{/if}