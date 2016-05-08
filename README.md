##Action Creator

En primer lugar creamos la acción (en realidad es un action creator), que es donde se encuentra el código encargado de conectarse al API. Como vamos a realizar una llamada asíncrona, en lugar de devolver un simple objeto JSON, nuestro action creator va a tener que devolver una función. Esta función la va a tener que procesar un middleware, redux-thunk. Por lo tanto vamos a tener que intalar este middleware, y también vamos a instalar el módulo axios para realizar peticiones HTTP. El código de nuestro action creator quedaría definido de la siguiente forma:

```javascript
import axios from 'axios'

export const SHOW_USERS = 'SHOW_USERS'

export function showUsers() {
    
    return (dispatch, getState) => {
        axios.get('http://jsonplaceholder.typicode.com/users')
            .then((response) => {
                dispatch( { type: SHOW_USERS, payload: response.data } ) 
            }) 
    }
    
} 
```


##Reducer

El siguiente fichero que vamos a crear es nuestro reducer. Este fichero no tiene nada especial. Se trata de un reducer estándar:

```javascript
import { SHOW_USERS } from '../actions'

const initialState = {
    list: []
}

export function showUsers(state = initialState, action) {
    
    switch (action.type) {
        case SHOW_USERS:
            return Object.assign({}, state, {list: action.payload})
        default:
            return state 
    }
    
}
```

##Component
Y por último, tenemos que modificar nuestro Componente para que se conecta a nuestro almacén de Redux y para que muestre el listado de usuarios en forma de tabla. El código del componente quedaría de la siguiente forma:

```javascript
import React from 'react';
import { Component } from 'react';
import { connect } from 'react-redux'
import { showUsers } from '../actions'
import { Table } from 'react-bootstrap'

class App extends Component {
  
  componentWillMount() {
    this.props.showUsers()
  }
  
  renderUsersList() {
    return this.props.users.map((user) => {
      return (
        <tr key={user.id}>
          <td>{user.id}</td>
          <td>{user.name}</td>
          <td>{user.email}</td>
        </tr>
      )
    })
  }
  
  render() {
    return (
      <div>
        <h2>Users List</h2>
        <Table responsive>
          <thead>
            <tr>
              <th>Id</th>
              <th>Name</th>
              <th>Email</th>
            </tr>
          </thead>
          <tbody>
            { this.renderUsersList() }
          </tbody>
        </Table>        
      </div>
    );
  }
}

function mapStateToProps(state) {
  return {
    users: state.user.list
  }
}

export default connect(mapStateToProps, { showUsers })(App)
```
