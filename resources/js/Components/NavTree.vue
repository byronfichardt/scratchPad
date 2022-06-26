<template>
    <Tree id="my-tree-id" ref="my-tree" :custom-options="myCustomOptions" :custom-styles="myCustomStyles" :nodes="treeDisplayData"></Tree>

    <nav class="context-menu">
        <ul class="context-menu__items">
            <li class="context-menu__item">
                <a href="#" class="context-menu__link">
                    <i class="fa fa-eye"></i> View Task
                </a>
            </li>
            <li class="context-menu__item">
                <a href="#" class="context-menu__link">
                    <i class="fa fa-edit"></i> Edit Task
                </a>
            </li>
            <li class="context-menu__item">
                <a href="#" class="context-menu__link">
                    <i class="fa fa-times"></i> Delete Task
                </a>
            </li>
        </ul>
    </nav>
</template>

<script setup>
import {onMounted, computed, ref} from "vue";
import Tree from 'vuejs-tree'
const vueSimpleContextMenuRef = ref(null)

onMounted(() => {
    let menu = document.querySelector(".context-menu");
    let menuState = 0;
    let taskItemClassName = "node";
    let activeClassName = "context-menu--active";

    const toggleMenuOn = () => {
        if ( menuState !== 1 ) {
            menuState = 1;
            menu.classList.add(activeClassName);
        }
    }

    const toggleMenuOff = () => {
        if ( menuState !== 0 ) {
            menuState = 0;
            menu.classList.remove(activeClassName);
        }
    }

    const getPosition = (e) => {
        var posx = 0;
        var posy = 0;

        if (!e) var e = window.event;

        if (e.pageX || e.pageY) {
            posx = e.pageX;
            posy = e.pageY;
        } else if (e.clientX || e.clientY) {
            posx = e.clientX + document.body.scrollLeft + document.documentElement.scrollLeft;
            posy = e.clientY + document.body.scrollTop + document.documentElement.scrollTop;
        }

        return {
            x: posx,
            y: posy
        }
    }

    const clickInsideElement = ( e, className ) => {
        let el = e.target;

        if ( el.classList.contains(className) ) {
            return el;
        } else {
            while ( el = el.parentNode ) {
                if ( el.classList && el.classList.contains(className) ) {
                    return el;
                }
            }
        }

        return false;
    }

    const contextMenuListener = (item) => {
        item.addEventListener('contextmenu', (e) => {
            if ( clickInsideElement( e, taskItemClassName ) ) {
                e.preventDefault();
                toggleMenuOn();
                positionMenu(e);
            }
        });
    }

    let clickCoords;
    let clickCoordsX;
    let clickCoordsY;
    let menuWidth;
    let menuHeight;
    let menuPosition;
    let menuPositionX;
    let menuPositionY;
    let windowWidth;
    let windowHeight;

    const positionMenu = (e) => {
        clickCoords = getPosition(e);
        clickCoordsX = clickCoords.x;
        clickCoordsY = clickCoords.y;

        menuWidth = menu.offsetWidth + 4;
        menuHeight = menu.offsetHeight + 4;

        windowWidth = window.innerWidth;
        windowHeight = window.innerHeight;

        if ( (windowWidth - clickCoordsX) < menuWidth ) {
            menu.style.left = windowWidth - menuWidth + "px";
        } else {
            menu.style.left = clickCoordsX + "px";
        }

        if ( (windowHeight - clickCoordsY) < menuHeight ) {
            menu.style.top = windowHeight - menuHeight + "px";
        } else {
            menu.style.top = clickCoordsY + "px";
        }
    }

    document.querySelector('body').addEventListener('click', (e) => {
        toggleMenuOff();
    });

    let taskItems = document.querySelectorAll(".node");

    for ( let i = 0, len = taskItems.length; i < len; i++ ) {
        let taskItem = taskItems[i];
        contextMenuListener(taskItem);
    }
})

const treeDisplayData = [
    {
        text: 'Root 1',
        state: { checked: false, selected: false, expanded: true },
        checkable: false,
        nodes: [
            {
                text: 'Child 1',
                state: { checked: false, selected: false, expanded: true },
                checkable: false,
                nodes: [
                    {
                        text: 'Grandchild 1',
                        checkable: false,
                        state: { checked: false, selected: false, expanded: false }
                    },
                    {
                        text: 'Grandchild 2',
                        checkable: false,
                        state: { checked: false, selected: false, expanded: false }
                    }
                ]
            },
            {
                text: 'Child 2',
                checkable: false,
                state: { checked: false, selected: false, expanded: false }
            }
        ]
    },
    {
        text: 'Root 2',
        checkable: false,
        state: { checked: false, selected: false, expanded: false }
    }
]
const myCustomStyles = computed(() => {
    return {
        tree: {
            style: {
                height: "auto",
                maxHeight: "300px",
                overflowY: "visible",
                display: "inline-block",
                textAlign: "left",
            },
        },
        row: {
            style: {
                width: "500px",
                cursor: "pointer",
            },
            child: {
                class: "",
                style: {
                    height: "35px",
                },
                active: {
                    style: {
                        height: "35px",
                    },
                },
            },
        },
        rowIndent: {
            paddingLeft: "20px",
        },
        text: {
            // class: "" // uncomment this line to overwrite the 'capitalize' class, or add a custom class
            style: {
                "padding-left": "5px"
            },
            active: {
                style: {
                    "font-weight": "bold",
                    color: "#2ECC71",
                },
            },
        },
    }
})
const myCustomOptions = computed(() => {
    return {
        treeEvents: {
            expanded: {
                state: false,
            },
            collapsed: {
                state: false,
            },
            selected: {
                state: true,
                fn: mySelectedFunction,
            },
            checked: {
                state: true,
                fn: myCheckedFunction,
            },
        },
        events: {
            expanded: {
                state: true,
            },
            selected: {
                state: true,
            },
            checked: {
                state: true,
            },
            editableName: {
                state: true,
                calledEvent: "expanded",
            },
        },
        addNode: {
            state: true,
            fn: addNodeFunction,
            appearOnHover: false,
        },
        editNode: { state: false, fn: null, appearOnHover: false },
        deleteNode: {
            state: true,
            fn: deleteNodeFunction,
            appearOnHover: true,
        },
        showTags: true
    }
})

const myCheckedFunction = function (nodeId, state) {
    console.log(`is ${nodeId} checked ? ${state}`);
    console.log(this.$refs["my-tree"].getCheckedNodes("id"));
    console.log(this.$refs["my-tree"].getCheckedNodes("text"));
}

const mySelectedFunction = function (nodeId, state) {
    console.log(`is ${nodeId} selected ? ${state}`);
    console.log(this.$refs["my-tree"].getSelectedNode());
}

const deleteNodeFunction = function (node) {
    const nodePath = this.$refs["my-tree"].findNodePath(node.id);
    const parentNodeId = nodePath.slice(-2, -1)[0];
    if (parentNodeId === undefined) {
        // 'root' node
        const nodeIndex =
            this.$refs["my-tree"].nodes.findIndex((x) => x.id !== node.id) - 1;
        this.$refs["my-tree"].nodes.splice(nodeIndex, 1);
    } else {
        // child node
        const parentNode = this.$refs["my-tree"].findNode(parentNodeId);
        const nodeIndex =
            parentNode.nodes.findIndex((x) => x.id !== node.id) - 1;
        parentNode.nodes.splice(nodeIndex, 1);
    }
    console.log("example: remove node", node.id);
}

const addNodeFunction = function (node) {
    const newNode = {
        text: "Grandchild 2",
        id: Math.floor(Math.random() * 100),
        state: { checked: false, selected: false, expanded: false },
    };
    console.log("example: add node", newNode);
    if (node.nodes === undefined) {
        node.nodes = [newNode];
    } else {
        node.nodes.push(newNode);
    }
}
</script>
<style scoped>
.context-menu {
    display: none;
    position: absolute;
    z-index: 10;
    padding: 12px 0;
    width: 240px;
    background-color: #fff;
    border: solid 1px #dfdfdf;
    box-shadow: 1px 1px 2px #cfcfcf;
}

.context-menu--active {
    display: block;
}

.context-menu__items {
    list-style: none;
    margin: 0;
    padding: 0;
}

.context-menu__item {
    display: block;
    margin-bottom: 4px;
}

.context-menu__item:last-child {
    margin-bottom: 0;
}

.context-menu__link {
    display: block;
    padding: 4px 12px;
    color: #0066aa;
    text-decoration: none;
}

.context-menu__link:hover {
    color: #fff;
    background-color: #0066aa;
}
</style>
