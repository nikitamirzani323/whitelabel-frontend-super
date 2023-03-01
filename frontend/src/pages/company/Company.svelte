<script>
    import Home from "./Home.svelte";
   
    
    export let table_header_font = "";
	export let table_body_font = "";
    
    let token = localStorage.getItem("token");
    let akses_page = false;
    let listHome = [];
    let record = "";
    let record_message = "";
    let totalrecord = 0;

    async function initapp() {
        const res = await fetch("/api/valid", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: "Bearer " + token,
            },
            body: JSON.stringify({
                page: "ADMIN-VIEW",
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
    async function initHome() {
        const res = await fetch("/api/company", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: "Bearer " + token,
            },
            body: JSON.stringify({
            }),
        });
        const json = await res.json();
        if (json.status == 200) {
            record = json.record;
            record_message = json.message;
            if (record != null) {
                totalrecord = record.length;
                let no = 0
                let home_statuscss = ""
                for (var i = 0; i < record.length; i++) {
                    no = no + 1;
                    if(record[i]["company_status"] == "Y"){
                        home_statuscss = "background:#FFEB3B;font-weight:bold;color:black;"
                    }else{
                        home_statuscss = "background:#E91E63;font-weight:bold;color:white;"
                    }
                    listHome = [
                        ...listHome,
                        {
                            home_no: no,
                            home_id: record[i]["company_id"],
                            home_idcurr: record[i]["company_idcurr"],
                            home_name: record[i]["company_name"],
                            home_owner: record[i]["company_owner"],
                            home_phone: record[i]["company_phone"],
                            home_email: record[i]["company_email"],
                            home_companyurl: record[i]["company_companyurl"],
                            home_start: record[i]["company_start"],
                            home_end: record[i]["company_end"],
                            home_status: record[i]["company_status"],
                            home_statuscss: home_statuscss,
                            home_create: record[i]["company_create"],
                            home_update: record[i]["company_update"],
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
    initapp()
</script>

{#if akses_page == true}
<Home
    on:handleRefreshData={handleRefreshData}
    {token}
    {table_header_font}
    {table_body_font}
    {listHome}
    {totalrecord}/>
{/if}