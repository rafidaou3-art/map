var simplemaps_countrymap_mapdata={
  main_settings: {
   //General settings
    width: "responsive", //'700' or 'responsive'
    background_color: "#FFFFFF",
    background_transparent: "yes",
    border_color: "#ffffff",
    
    //State defaults
    state_description: "State description",
    state_color: "#88A4BC",
    state_hover_color: "#3B729F",
    state_url: "",
    border_size: 1.5,
    all_states_inactive: "no",
    all_states_zoomable: "yes",
    
    //Location defaults
    location_description: "Location description",
    location_url: "",
    location_color: "#FF0067",
    location_opacity: 0.8,
    location_hover_opacity: 1,
    location_size: 25,
    location_type: "square",
    location_image_source: "frog.png",
    location_border_color: "#FFFFFF",
    location_border: 2,
    location_hover_border: 2.5,
    all_locations_inactive: "no",
    all_locations_hidden: "no",
    
    //Label defaults
    label_color: "#ffffff",
    label_hover_color: "#ffffff",
    label_size: 16,
    label_font: "Arial",
    label_display: "auto",
    label_scale: "yes",
    hide_labels: "no",
    hide_eastern_labels: "no",
   
    //Zoom settings
    zoom: "yes",
    manual_zoom: "yes",
    back_image: "no",
    initial_back: "no",
    initial_zoom: "-1",
    initial_zoom_solo: "no",
    region_opacity: 1,
    region_hover_opacity: 0.6,
    zoom_out_incrementally: "yes",
    zoom_percentage: 0.99,
    zoom_time: 0.5,
    
    //Popup settings
    popup_color: "white",
    popup_opacity: 0.9,
    popup_shadow: 1,
    popup_corners: 5,
    popup_font: "12px/1.5 Verdana, Arial, Helvetica, sans-serif",
    popup_nocss: "no",
    
    //Advanced settings
    div: "map",
    auto_load: "yes",
    url_new_tab: "no",
    images_directory: "default",
    fade_time: 0.1,
    link_text: "View Website",
    popups: "detect",
    state_image_url: "",
    state_image_position: "",
    location_image_url: ""
  },
  state_specific: {
    LBAK: {
      name: "Akkar",
      url: "https://sites.google.com/view/letsvotelb/candidates/akkar?authuser=0",
      description: "66 Candidates"
    },
    LBAS: {
      name: "North",
      url: "https://sites.google.com/view/letsvotelb/candidates/north?authuser=0",
      description: "59 Candidates"
    },
    LBBA: {
      name: "Beirut",
      url: "https://sites.google.com/view/letsvotelb/candidates/beirut?authuser=0",
      description: "90 Candidates"
    },
    LBBH: {
      name: "Baalbek-El Hermel",
      url: "https://sites.google.com/view/letsvotelb/candidates/baalbek-el-hermel?authuser=0",
      description: "48 Candidates"
    },
    LBBI: {
      name: "Bekaa",
      url: "https://sites.google.com/view/letsvotelb/candidates/bekaa?authuser=0",
      description: "105 Candidates"
    },
    LBJA: {
      name: "South",
      url: "https://sites.google.com/view/letsvotelb/candidates/south?authuser=0",
      description: "14 Candidate"
    },
    LBJL: {
      name: "Mount Lebanon",
      url: "https://sites.google.com/view/letsvotelb/candidates/mount-lebanon?authuser=0",
      description: "207 Candidates"
    },
    LBNA: {
      name: "El Nabatieh",
      url: "https://sites.google.com/view/letsvotelb/candidates/el-nabatieh?authuser=0",
      description: "56 Candidates"
    }
  },
  locations: {
    "0": {
      name: "Beirut",
      lat: "33.871944",
      lng: "35.509722"
    }
  },
  labels: {
    LBAK: {
      name: "Akkar",
      parent_id: "LBAK"
    },
    LBAS: {
      name: "North",
      parent_id: "LBAS"
    },
    LBBA: {
      name: "Beirut",
      parent_id: "LBBA"
    },
    LBBH: {
      name: "Baalbek-El Hermel",
      parent_id: "LBBH"
    },
    LBBI: {
      name: "Bekaa",
      parent_id: "LBBI"
    },
    LBJA: {
      name: "South",
      parent_id: "LBJA"
    },
    LBJL: {
      name: "Mount Lebanon",
      parent_id: "LBJL"
    },
    LBNA: {
      name: "El Nabatieh",
      parent_id: "LBNA"
    }
  },
  legend: {
    entries: []
  },
  regions: {}
};
